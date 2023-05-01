---
layout: post
title: "Builder pattern"
date: 2023-05-03 07:00:00 -0000
categories: ["design patterns"]
---
When building objects, we might think that an [Abstract Factory](/posts/abstract-factory-pattern/) will perform all needed operations to create one. And so it does. But Abstract Factory is great at constructing simple components. When it comes to composites, this approach may be to simple and the building process to complicated.
When we want to dynamically build objects on demand the worse it gets. For that particular reason the Builder Design Pattern has been invented.

# Core concept
As the Abstract Factory is used to create families of similar objects, the Builder is used to **compose** a bigger object out of smaller ones using different factories. Usually a `Builder` comes in pair with a `Director`, who orchestrates how the Builder should behave.

# Simple example 
Let's take advantage of this idea and create a utility library. This library will build a logging mechanism, so that we can log what is happening in our program during execution. We will have different builders which would deliver different types of loggers. From a modest one, to a blown out one. You  can find the project in my [GitHub page](https://github.com/grzegorz-grzeda/builder-pattern-example-cpp).

The Logger contains several content providers. These would provide info about:

* date/time/timestamp,
* message prefix text,
* message postfix text.

There also is a data sink, which receives the constructed string and channels it to a certain output.

For each provider/data sink there would be a [Null Object](/posts/null-object-pattern/) implementation to provide coherent dummy functionality.

### Content providers
```c++
struct IContentProvider
{
	virtual ~IContentProvider() {}
	virtual void set(std::string) {}
	virtual std::string getContent() = 0;
};
```
Simple interface with a content getter and a dummy setter.
```c++
struct NullContentProvider : IContentProvider
{
	std::string getContent()
	{
		return "";
	}
};
```
This null content provider provides an empty string.
```c++
struct TextProvider : IContentProvider
{
	TextProvider(std::string text) : text(text) {}

	std::string getContent()
	{
		return text;
	}

private:
	std::string text;
};
```
This provider returns a static text set on creation. Useful for tags.
```c++
#pragma once

#include "IContentProvider.hpp"
#include <ctime>
#include <sstream>
#include <chrono>
#include <iomanip>

struct DateTimeProvider : IContentProvider
{
	std::string getContent()
	{
		auto now = std::chrono::system_clock::now();
		auto in_time_t = std::chrono::system_clock::to_time_t(now);
		auto tm = std::localtime(&in_time_t);

		std::stringstream result;

		result << std::put_time(tm, "%Y:%m:%d %H:%M:%S ");

		return result.str();
	}
};
```
This returns a string with the current date-time i na human readable format.

### Data sinks

These structures wil handle the output of constructed messages. We prepare two: a standard output data sink and a null one for dummy (black hole).
```c++
struct IDataSink
{
	virtual ~IDataSink() {}

	virtual void printLine(std::string) = 0;
};
```
```c++
struct NullDataSink : IDataSink
{
	void printLine(std::string) {}
};
```
```c++
struct CoutDataSink : IDataSink
{
	void printLine(std::string text)
	{
		std::cout << text << std::endl;
	}
};
```

### The `Logger`

The `Logger` structure is a composition of components above. When a new message arrives, it simply asks all the content providers for content. Lastly, it composes the message and logs it to the sink.
```c++
struct Logger
{
	Logger()
		: dateTimeProvider(new NullContentProvider),
		  prefixProvider(new NullContentProvider),
		  postfixProvider(new NullContentProvider),
		  dataSink(new NullDataSink)
	{
	}

	void log(std::string message)
	{
		std::string msg;

		msg = dateTimeProvider->getContent();
		msg += prefixProvider->getContent();
		msg += message;
		msg += postfixProvider->getContent();

		dataSink->printLine(msg);
	}

	void setDateTimeProvider(IContentProvider *dtp)
	{
		dateTimeProvider = dtp;
	}

	void setPrefixProvider(IContentProvider *prefix)
	{
		prefixProvider = prefix;
	}

	void setPostfixProvider(IContentProvider *postfix)
	{
		postfixProvider = postfix;
	}

	void setDataSink(IDataSink *ds)
	{
		dataSink = ds;
	}

private:
	IContentProvider *dateTimeProvider;
	IContentProvider *prefixProvider;
	IContentProvider *postfixProvider;
	IDataSink *dataSink;
};
```

### The builders
These are the crux of this blog post. We prepare two versions of a logger: a modest one and a full text option.
For each version we will provide a builder. Each builder must implement an abstract builder. We will write a director, which will abstractly handle the building process.
```c++
struct ALoggerBuilder
{
	void createNewLogger()
	{
		logger = new Logger();
	}

	void setLoggerLogic()
	{
		logger->setDateTimeProvider(getDateTimeProvider());
		logger->setPrefixProvider(getPrefixProvider());
		logger->setPostfixProvider(getPostfixProvider());
		logger->setDataSink(getDataSink());
	}

	Logger *getLogger()
	{
		return logger;
	}

protected:
	virtual IContentProvider *getDateTimeProvider() = 0;
	virtual IContentProvider *getPrefixProvider() = 0;
	virtual IContentProvider *getPostfixProvider() = 0;
	virtual IDataSink *getDataSink() = 0;

	Logger *logger;
};
```
```c++
struct ModestLoggerBuilder : ALoggerBuilder
{
private:
	IContentProvider *getDateTimeProvider()
	{
		static auto ncp = new NullContentProvider();
		return ncp;
	}
	IContentProvider *getPrefixProvider()
	{
		static auto ncp = new NullContentProvider();
		return ncp;
	}
	IContentProvider *getPostfixProvider()
	{
		static auto ncp = new NullContentProvider();
		return ncp;
	}
	IDataSink *getDataSink()
	{
		static auto cds = new CoutDataSink;
		return cds;
	}
};
```
```c++
struct FullOptionLoggerBuilder : ALoggerBuilder
{
private:
	IContentProvider *getDateTimeProvider()
	{
		static auto dtp = new DateTimeProvider();
		return dtp;
	}
	IContentProvider *getPrefixProvider()
	{
		static auto pp = new TextProvider("[LOG] >>> ");
		return pp;
	}
	IContentProvider *getPostfixProvider()
	{
		static auto pp = new TextProvider(" <<<");
		return pp;
	}
	IDataSink *getDataSink()
	{
		static auto cds = new CoutDataSink;
		return cds;
	}
};
```

### The director and end test
The Director is the one making the builder dance. It performs all the needed building process and returns the composed logger. The `main` function build two versions of the logger - a modest one and full option.
```c++
struct LoggerBuildingDirector
{
	LoggerBuildingDirector() : builder(nullptr) {}

	void setBuilder(ALoggerBuilder *b)
	{
		builder = b;
	}

	void buildLogger()
	{
		builder->createNewLogger();
		builder->setLoggerLogic();
	}

	Logger *getLogger()
	{
		return builder->getLogger();
	}

private:
	ALoggerBuilder *builder;
};
```
```c++
int main()
{
	std::cout << "Hello in Builder Design Pattern example";
	std::cout << std::endl;

	auto mlb = new ModestLoggerBuilder;
	auto folb = new FullOptionLoggerBuilder;
	auto lbd = new LoggerBuildingDirector;

	lbd->setBuilder(mlb);
	lbd->buildLogger();

	auto l = lbd->getLogger();
	l->log("Logging once");

	delete l;

	lbd->setBuilder(folb);
	lbd->buildLogger();

	l = lbd->getLogger();
	l->log("Logging twice");

	return 0;
}
```

The console output:
```shell
Hello in Builder Design Pattern example
Logging once
2019/12/22 04:28:21.543 [LOG] >>> Logging twice <<<
```

# Tinker with it!

You can expand the functionality in a variety of ways:

* add other providers and builders (e.g. timestamp),
* add file data sink or web socket data sink to channel data to other places (even write a proxy data sink, which could send data to multiple other data sinks).