---
layout: post
title: "NULL object pattern"
date: 2023-04-30 07:00:00 -0000
categories: ["design patterns"]
---
When you want to remain a consistent structure throughout your application, you
may run into a problem. How to introduce a fake or void functionality? Why would you
do such a thing in the first place?

## The Logger example
Lets consider a simple application, where you perform some action and want to log it 
on the standard output. Let the app be a simple calculator, performing ordinary tasks:

* input the first operand
* input the second operand
* compute
* present the result of the operation

On each step we want the operation to be logged on the standard output.

By the way, you can find the project on [my GitHub site](https://github.com/grzegorz-grzeda/null-object-pattern-example-cpp).

The project has the structure:

```shell
null-object-pattern-example-cpp \
	Makefile
	main.cpp
	ILog.hpp
	ConsoleLog.hpp
	CalcModel.hpp
```

It consists of the [typical](/posts/simple-makefile-for-starters/) Makefile. For sake of 
simplicity, I omitted the other design patterns I could use here.

The `ILog` interface, consists of a single method to write somewhere:
```c++
#ifndef ILOG_HPP__
#define ILOG_HPP__

struct ILog
{
	virtual ~ILog() {}

	virtual void l(const char *) = 0;
};

#endif // !ILOG_HPP__
```

The `ConsoleLog` implementation of the `ILog` simply prints content to `stdout` with a
informative prefix:
```c++
#ifndef CONSOLELOG_HPP__
#define CONSOLELOG_HPP__

#include "ILog.hpp"
#include <iostream>

struct ConsoleLog : ILog
{
	void l(const char *text)
	{
		std::cout << "[LOG] " << text << std::endl;
	}
};

#endif // !CONSOLELOG_HPP__
```

The `CalcModel` class is a simple calculator. What is important here is the logging part. 
Every step is logged for our purposes:
```c++
#ifndef CALCMODEL_HPP__
#define CALCMODEL_HPP__

#include "ILog.hpp"

struct CalcModel
{
	CalcModel(ILog *log) : log(log), a(0), b(0), result(0) {}

	void setOperandA(int a)
	{
		this->a = a;
		log->l("Setting operand A");
	}

	void setOperandB(int b)
	{
		this->b = b;
		log->l("Setting operand B");
	}

	void compute()
	{
		result = a + b;
		log->l("Computing");
	}

	int getResult()
	{
		log->l("Returning result");
		return result;
	}

private:
	ILog *log;
	int a;
	int b;
	int result;
};

#endif // !CALCMODEL_HPP__
```
Of course, I could push each logging operation into a separate private method and check there
if the log pointer is valid, but that is not the case here. The `CalcModel` assumes, that the `log`
pointer passed through the constructor is valid.

Finally, the `main` file. Here we fetch input data from the command line parameters, then
perform needed operations. Finally the program prints the result. From here, we don't know
anything about the fact, that the `CalcModel` does some logging:
```c++
#include "ConsoleLog.hpp"
#include "CalcModel.hpp"
#include <cstdlib>
#include <iostream>

int main(int argc, char *argv[])
{
	if (argc < 3)
		return -1;

	int a = std::atoi(argv[1]);
	int b = std::atoi(argv[2]);

	auto log = new ConsoleLog();
	auto cm = new CalcModel(log);

	cm->setOperandA(a);
	cm->setOperandB(b);
	cm->compute();

	std::cout << "Result: " << cm->getResult() << std::endl;

	delete cm;
	delete log;

	return 0;
}
```

The result of operation:
```shell
>null-object-pattern-example-cpp.exe 1 2
[LOG] Setting operand A
[LOG] Setting operand B
[LOG] Computing
[LOG] Returning result
3
```
We see, that the program logs what it does. The final line contains the desired result.
Everything is smooth...

Now, one of your clients calls you and says: 'But we don't want that log-thing at all! 
We know what we are doing. Please cut it out for us...'. You think, how to do that without
shaking the architecture of the application. So you come up with an idea...

## Solution
The idea is simple, elegant and doesn't compromise the whole architecture of the application.
Only the `main` source file would be affected, leaving other components untouched.

You write another implementation of the `ILog`, the `NullLog`:
```c++
#ifndef NULLLOG_HPP__
#define NULLLOG_HPP__

#include "ILog.hpp"

struct NullLog : ILog
{
	void l(const char *text)
	{
		(void)text;
	}
};

#endif // !NULLLOG_HPP__
```
Here, the `text` is voided. The component does nothing. You need to add proper code into the `main`:
```c++
#include "ConsoleLog.hpp"
#include "NullLog.hpp"
#include "CalcModel.hpp"
#include <cstdlib>
#include <iostream>

#define DONT_USE_LOG

int main(int argc, char *argv[])
{
	if (argc < 3)
		return -1;

	int a = std::atoi(argv[1]);
	int b = std::atoi(argv[2]);

#ifdef DONT_USE_LOG
	auto log = new NullLog();
#else
	auto log = new ConsoleLog();
#endif // DONT_USE_LOG
	auto cm = new CalcModel(log);

	cm->setOperandA(a);
	cm->setOperandB(b);
	cm->compute();

	std::cout << "Result: " << cm->getResult() << std::endl;

	delete cm;
	delete log;

	return 0;
}
```

The `#define` and `#ifdef` preprocessor macros specify when to supply the `NullLog` and when
the standard `ConsoleLog`.

The result:
```shell
>null-object-pattern-example-cpp.exe 1 2
Result: 3
```

As you see, the pattern gave us the freedom in expanding the application by... doing nothing.

## Benefits
The Null Object pattern gives us:

* the flexibility of quickly deactivating certain app functionalities, by introducing mocks,
doing nothing. Just do it through an interface, common for each functionality,
* it naturally comes in the [test driven development](/posts/tdd-rules/) as a simple mock of a
functionality,
* we can implement the pattern as a **part** of a class, extending the functionality into nothing.
This means, that we actually use the `Strategy` DP,
* we can use the pattern to introduce a null behavior, when using the `Visitor` DP.

## Traps
We need to take care:

* not to overload the app with many object doing nothing. As each instance of a null object does 
nothing, we can supply them by a `Singleton` design pattern,
* using preprocessor macros like `#define` and `#ifdef` isn't a good idea - they spread out fast,
and the project in time becomes unmanagable. Use `Abstract Factory` DP instead.

## Conclusion
What may seem over-designed, actually prevents the architecture from rotting, code management from
chaotic decisions and the source management system from many 'special' branches of compilation.

After all, there is a lot when it comes to no doing anything.