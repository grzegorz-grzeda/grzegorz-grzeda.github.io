---
layout: post
title: "Abstract factory pattern"
date: 2023-04-27 07:00:00 -0000
categories: "design patterns"
---
The main idea behind the abstract factory is to obscure the creation of objects. 
Why to complicate this basic concept? Because of the potential 'multi-platform code
compilation'.

When our code needs to be recompiled with multiple wariants of classes implementing 
same interfaces, there is a high risk of spreading `#ifdef...#elseif...#endif` macros
with the `new` and `delete` operators all over the application code. 

Abstract Factory prevents from such spread offering a collective object creation approach.
We actually get a factory, producing objects implementing same interfaces, but don't need
to worry (on a certain level) about the details of which concrete class should be 
instantiated.

## Two variant example
On [my GitHub page](https://github.com/grzegorz-grzeda) i published the following 
[example project](https://github.com/grzegorz-grzeda/abstract-factory-pattern-example-cpp).
The project is compiled using [a simple Makefile](/posts/simple-makefile-for-starters/).

This is a simple application, where we create two types of objects:

* a DOER,
* a WAITER (which waits for something).

Also, we have an `App` class, which is **platform agnostic**. This means, that the code
inside does know **nothing** about implementation details of the `Doer` and `Waiter`.
The only parts 'aware' of those details would be: `main()` and factories producing
those details.

The project will consist of factories:

* the `IFactory` interface,
* `SmallFactory : IFactory` producing the:
	* `SmallDoer` and
	* `SmallWaiter`,
* `OtherFactory : IFactory` producing the:
	* `OtherDoer` and
	* `OtherWaiter`,
* `SystemFactory : IFactory` generalizing those two factories above in order to provide a
simple point of dependency injection.

## The code
The whole code is just a presentation of what we can achieve with the abstract factories.
The 'doers' and 'waiters' can be everything in real life: DB connectors, hardware drivers,
TDD mocks, etc.

The Doers and Waiters:
```c++
// IDoer
struct IDoer
{
	virtual ~IDoer() {}
	virtual void doIt() = 0;
};
```
```c++
// SmallDoer
#include "IDoer.hpp"
#include <iostream>

struct SmallDoer : IDoer
{
	void doIt()
	{
		std::cout << "[SMALL DOER]: doing... something small" << std::endl;
	}
};
```
```c++
// OtherDoer
#include "IDoer.hpp"
#include <iostream>

struct OtherDoer : IDoer
{
	void doIt()
	{
		std::cout << "[OTHER DOER]: doing... something else" << std::endl;
	}
};
```
```c++
// IWaiter
struct IWaiter
{
	virtual ~IWaiter() {}
	virtual void waitForIt() = 0;
};
```
```c++
// SmallWaiter
#include "IWaiter.hpp"
#include <iostream>

struct SmallWaiter : IWaiter
{
	void waitForIt()
	{
		std::cout << "[SMALL WAITER]: waiting for... something small" << std::endl;
	}
};
```
```c++
// OtherWaiter
#include "IWaiter.hpp"
#include <iostream>

struct OtherWaiter : IWaiter
{
	void waitForIt()
	{
		std::cout << "[OTHER WAITER]: waiting for... something else" << std::endl;
	}
};
```

Those classes simply print their behavior onto `stdout`, so that you can see which
implementation is currently being instantiated.

The factories:
```c++
// IFactory
#include "IDoer.hpp"
#include "IWaiter.hpp"

struct IFactory
{
	virtual ~IFactory() {}
	virtual IDoer *getNewDoer() = 0;
	virtual IWaiter *getNewWaiter() = 0;
};
```
The Factory interface tells us, that **a** factory will deliver both, the `IDoer` and
`IWaiter` objects from **the same architecture/approach**. The `SmallFactory` will deliver
the `SmallDoer` and `SmallWaiter` and the `OtherFactory` accordingly the `Other...` 
architecture.
```c++
// SmallFactory
#include "SmallDoer.hpp"
#include "SmallWaiter.hpp"
#include "IFactory.hpp"

struct SmallFactory : IFactory
{
	IDoer *getNewDoer()
	{
		return new SmallDoer();
	}
	IWaiter *getNewWaiter()
	{
		return new SmallWaiter();
	}
};
```
```c++
// OtherFactory
#include "OtherDoer.hpp"
#include "OtherWaiter.hpp"
#include "IFactory.hpp"

struct OtherFactory : IFactory
{
	IDoer *getNewDoer()
	{
		return new OtherDoer();
	}
	IWaiter *getNewWaiter()
	{
		return new OtherWaiter();
	}
};
```
```c++
// SystemFactory
#include "SmallFactory.hpp"
#include "OtherFactory.hpp"

struct SystemFactory : IFactory
{
	SystemFactory(bool isSmallArchitecture)
	{
		if (isSmallArchitecture)
			f = new SmallFactory;
		else
			f = new OtherFactory;
	}
	~SystemFactory() { delete f; }

	IDoer *getNewDoer()
	{
		return f->getNewDoer();
	}

	IWaiter *getNewWaiter()
	{
		return f->getNewWaiter();
	}

private:
	IFactory *f;
};
```
The `SystemFactory` is where the magic happens! Here, depending on what we provided
for the constructor, the according factory is created. Next, this module will create
objects aligned to the initial setting throughout the application.

The app and `main()`:
```c++
//app
#include "IDoer.hpp"
#include "IWaiter.hpp"

struct App
{
	App(IDoer *d, IWaiter *w) : d(d), w(w) {}

	void run()
	{
		d->doIt();
		w->waitForIt();
		w->waitForIt();
		d->doIt();
		w->waitForIt();
	}

private:
	IDoer *d;
	IWaiter *w;
};
```
The `App` doesn't know (or care) what was the **precise architecture** of the supplied `IDoer`
and `IWaiter` objects. It just operates on them as needed.

```c++
//main()
#include "SystemFactory.hpp"
#include "App.hpp"

int main(int argc, char *argv[])
{
	auto factory = new SystemFactory(true);
	auto doer = factory->getNewDoer();
	auto waiter = factory->getNewWaiter();

	auto app = new App(doer, waiter);

	app->run();

	delete app;
	delete waiter;
	delete doer;
	delete factory;

	return 0;
}
```

The `main()` creates the system factory, with the concrete setting. Currently 
it is set on providing the `Small...` architecture to the `App` object. Notice that here,
both the `doer` and `waiter` pointers are indeed `IDoer*` and `IWaiter*` hiding under the
`auto` keyword (check it on your own).

When you compile and run the app, you should see:
```shell
>abstract-factory-pattern-example-cpp.exe
[SMALL DOER]: doing... something small
[SMALL WAITER]: waiting for... something small
[SMALL WAITER]: waiting for... something small
[SMALL DOER]: doing... something small
[SMALL WAITER]: waiting for... something small
```

When you change the `true` to `false`, the program changes its behavior to:
```shell
>abstract-factory-pattern-example-cpp.exe
[OTHER DOER]: doing... something else
[OTHER WAITER]: waiting for... something else
[OTHER WAITER]: waiting for... something else
[OTHER DOER]: doing... something else
[OTHER WAITER]: waiting for... something else
```

## Benefits
The Abstract Factory pattern gives us:

* a level of indirection - objects are provided through common interfaces,
* all the `#if...#elseif...#endif` macros with `new` and `delete` are transformed into
`if()...else...` statements in code, which can be troubleshooted in the debugger or even
during compile time,
* well organized abstract code can even enable runtime object switching (with
objects from different factories).

## Traps
You need to take care:

* be consistent in the creation of all those factories, as multi-variant cases arise,
* don't overshoot with object creation - some can (or should) be created on the fly, while
others can (or should) be **Singletons**, provided by **Proxies** or another layer of indirection,
* often overused Factory Methods should evolve into Abstract Factories.

## Conclusion
Abstract Factory is a great way to group and manage your dependency injections with ease. No
more spreaded `if's` with custom object creation. No more rabbit chases about those `if's` you've
forgotten!