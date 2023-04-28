---
layout: post
title: "Observer design pattern"
date: 2023-05-15 07:00:00 -0000
categories: "design patterns"
---

One of the most commonly (and usually subconsciously) used pattern. Present especially in 
GUI frameworks, but not only. Everywhere, where there is a data producer and multiple data consumers.
All kind of event generators and event listeners. Let's build some!

## C++ example
You can find the complete project [here](https://github.com/grzegorz-grzeda/observer-pattern-example-cpp).
I was using the [simple Makefile](/posts/simple-makefile-for-starters/) to automatically compile and
link my code. Just remember - this is a C++ example, so the compiler and linker in the Makefile should both be
set to `g++`.

The project consists of files:
```shell
observer-pattern-example-cpp \
	main.cpp
	IDataListener.hpp
	IErrorListener.hpp
	DecPrinter.hpp
	HexPrinter.hpp
	OctPrinter.hpp
	ErrorPrinter.hpp
	InputParser.hpp
```

Our program will try to fetch numbers from the program parameters (`argv[]`) and present those numbers in some ways. If the provided string
is not a number, errors will be displayed.

First, the interfaces for the event listeners:

```c++
// IDataListener.hpp
#ifndef IDATALISTENER_HPP__
#define IDATALISTENER_HPP__

struct IDataListener
{
	virtual ~IDataListener() {}

	virtual void dataReceived(int number) = 0;
}

#endif // !IDATALISTENER_HPP__
```

```c++
//IErrorListener.hpp
#ifndef IERRORLISTENER_HPP__
#define IERRORLISTENER_HPP__

struct IErrorListener
{
	virtual ~IErrorListener() {}

	virtual void errorReceived(const char *message) = 0;
}

#endif // !IERRORLISTENER_HPP__
```

Both similar in structure. With single methods to implement. The listeners will be collected into lists and invoked sequentially. Each 
listener needs to be registered in order to hear the incoming data he wishes to process.

The listeners:
```c++
// DecPrinter.hpp
#ifndef DECPRINTER_HPP__
#define DECPRINTER_HPP__

#include "IDataListener.hpp"
#include <iostream>
#include <iomanip>

struct DecPrinter : IDataListener
{
	void dataReceived(int number)
	{
		std::cout << "DEC: "
				  << std::dec
				  << std::setw(8)
				  << number << std::endl;
	}
};

#endif // !DECPRINTER_HPP__
```
```c++
// HexPrinter.hpp
#ifndef HEXPRINTER_HPP__
#define HEXPRINTER_HPP__

#include "IDataListener.hpp"
#include <iostream>
#include <iomanip>

struct HexPrinter : IDataListener
{
	void dataReceived(int number)
	{
		std::cout << "HEX: "
				  << std::hex
				  << std::uppercase
				  << std::setfill('0')
				  << std::setw(8)
				  << number
				  << std::endl;
	}
};

#endif // !HEXPRINTER_HPP__
```
```c++
// OctPrinter.hpp
#ifndef OCTPRINTER_HPP__
#define OCTPRINTER_HPP__

#include "IDataListener.hpp"
#include <iostream>
#include <iomanip>

struct OctPrinter : IDataListener
{
	void dataReceived(int number)
	{
		std::cout << "OCT: "
				  << std::oct
				  << std::setfill('0')
				  << std::setw(8)
				  << number
				  << std::endl;
	}
};

#endif // !OCTPRINTER_HPP__
```
```c++
// ErrorPrinter.hpp
#ifndef ERRORPRINTER_HPP__
#define ERRORPRINTER_HPP__

#include "IErrorListener.hpp"
#include <iostream>

struct ErrorPrinter : IErrorListener
{
	void errorReceived(const char *message)
	{
		std::cout << "ERROR! "
				  << message
				  << std::endl;
	}
};

#endif // !ERRORPRINTER_HPP__
```

These above are simples modules printing to the standard output in a formatted way. The passed number is represented in a different radix.
I used the `<iomanip>` functionalities to pad the output data with zeros.

The main program happens in the `InputParser` structure. Here, we add the listeners to the system. Their pointers are stored in vectors.
The `printData()` and `printError()` methods are iterating through the listeners and invoke their proper methods. Notice, that in no 
place the `InputParser` has to know anything about the listener and how they work.

The parsing is simple - if the input is not a number - print an error. If it is a number - display it. For the parsing I used `stringstream` from
the `std` namespace.

```c++
// InputParser.hpp
#ifndef INPUTPARSER_HPP__
#define INPUTPARSER_HPP__

#include <vector>
#include <sstream>
#include <string>
#include "IDataListener.hpp"
#include "IErrorListener.hpp"

struct InputParser
{
	void addDataListener(IDataListener *listener)
	{
		dataListeners.push_back(listener);
	}

	void addErrorListener(IErrorListener *listener)
	{
		errorListeners.push_back(listener);
	}

	int parse(int argc, char *argv[])
	{
		int result = 0;

		if (argc < 2)
		{
			printError("No data :(");

			result = -1;
		}
		else
		{
			for (int i = 1; i < argc; i++)
			{
				char check;
				int number;
				std::istringstream s(argv[i]);
				s >> number;

				if (s.fail() || s.get(check))
					printError(argv[i] + std::string("is not a number"));
				else
					printData(number);
			}
		}

		return result;
	}

private:
	std::vector<IDataListener *> dataListeners;
	std::vector<IErrorListener *> errorListeners;

	void printError(std::string message)
	{
		for (auto listener : errorListeners)
			listener->errorReceived(message.c_str());
	}

	void printData(int data)
	{
		for (auto listener : dataListeners)
			listener->dataReceived(data);
	}
};

#endif // !INPUTPARSER_HPP__
```

Lastly, the `main()`. Here we just initialize the listeners and the parser. After binding everything, we try to parse the provided data.
Before exit, we delete what we've created. We don't have to do this explicitly, because, the system would release all of the occupied 
memory when the application finishes. Yet, I want my code to stay neat.

```c++
// main.cpp
#include "InputParser.hpp"
#include "DecPrinter.hpp"
#include "HexPrinter.hpp"
#include "OctPrinter.hpp"
#include "ErrorPrinter.hpp"

int main(int argc, char *argv[])
{
	InputParser ip;

	auto dec = new DecPrinter();
	auto hex = new HexPrinter();
	auto oct = new OctPrinter();
	auto err = new ErrorPrinter();

	ip.addDataListener(dec);
	ip.addDataListener(hex);
	ip.addDataListener(oct);
	ip.addErrorListener(err);

	auto result = ip.parse(argc, argv);

	delete err;
	delete oct;
	delete hex;
	delete dec;

	return result;
}
```

## Expansion
Nothing stands in our way to develop a new listener! Maybe, now we want to... write to a log file! No worries!

```c++
// FileLogListener.hpp
#ifndef FILELOGPRINTER_HPP__
#define FILELOGPRINTER_HPP__

#include "IDataListener.hpp"
#include <fstream>
#include <iomanip>
#include <ctime>
#include <chrono>

struct FileLogPrinter : IDataListener
{
	void dataReceived(int number)
	{
		std::fstream fs("log.txt",
						std::fstream::out | std::fstream::app);

		auto now = std::chrono::system_clock::now();
		auto in_time_t = std::chrono::system_clock::to_time_t(now);
		auto tm = std::localtime(&in_time_t);

		fs << "["
		   << std::put_time(tm, "%Y:%m:%d %H:%M:%S")
		   << "] "
		   << number << std::endl;

		fs.close();
	}
};

#endif // !FILELOGPRINTER_HPP__
```

This listener opens a file named `log.txt`. Gets the current time, formats it and writes to the file. 
The file stream is set to `append` mode, which 
means, the file won't be truncated on opening, but extended with the streamed content.

Of course, we need to add the listener to the `InputParser`, so don't forget to write some code in the
`main` function.

## Benefits
The observer patter decouples the code:

* the data generator from data listeners,
* details how to handle generated data - every listener does this in his own fashion,
* implementing a certain observer interface assures the generator, that each listener knows what to do,
* we can dynamically add/modify the generator-observer structure.

## Traps
We need to take care:

* if a listener is added to multiple generators, at first it doesn't know from which one the data came (need to add meta data),
* the building mechanism (`main()`) has to know precisely which listener add where. There is no `message differentiating`.

## Conclusion
The Observer Pattern is very useful and decouples processing from presentation. The application becomes extendable with many independent 
components, which don't need to know about each other.