---
layout: post
title: "Visitor design pattern"
date: 2023-05-21 07:00:00 -0000
categories: "design patterns"
---

When we have a collection of concrete classes and we want to perform some operations, we usually need to manually invoke each method 'by hand'. Usually we create a common interface and place methods to be implemented. Them we can invoke those methods in a more generic fashion. 

But this approach introduces threats to the project. Mainly, we force all classes to implement some methods that not necessarily align with all of those classes logic. This lie has to propagate throughout the architecture.

There is a solution to this. We can invert the direction of control and introduce a class that is **specifically** designed to handle incompatible (by type) objects that need to be handled closely. This is the crux of the Visitor Design Pattern.

We indeed create a common interface, but only to deliver a method, through which the object in question would need to `accept` a visitor. It is up to the visitor, what it would do, when the invitation would be accepted.

# The project
We will create [a simple project](https://github.com/grzegorz-grzeda/visitor-pattern-example-cpp.git) using the [list component](/posts/simple-list-for-embedded-c-plus-plus/) from one of earlier posts.

We will have three types of objects: a `Doer`, `Thinker` and `Writer`. Each will perform specific operations, uncommon for each other. Yet, each will have to implement a `IElement` interface, where each could `accept` a `IVisitor`. All will be gathered under a `List`, so that the visitor could conveniently visit each object.

```c++
#pragma once
#include "IVisitor.hpp"

struct IElement
{
	virtual ~IElement() {}

	virtual void accept(IVisitor *v) = 0;
};
```
Here, the `IElement` has a method for accepting a visitor. Nothing fancy so far.

```c++
#pragma once

struct Doer;
struct Thinker;
struct Writer;

struct IVisitor
{
	virtual ~IVisitor() {}

	virtual void visit(Doer *) = 0;
	virtual void visit(Thinker *) = 0;
	virtual void visit(Writer *) = 0;
};
```
This starts to look interesting! We declare an `IVisitor` type and say, that somewhere there in the code, there are some structure types we would like to use. We **cannot** write `#include "Doer.hpp` and so on. 

Why? Because in `Doer.hpp` there will be a `#include "IElement.hpp"`. In `IElement.hpp` there is a `#include "IVisitor.hpp"`. In each header file we've said `#pragma once`, so the compiler would not include further. We would get an infinite inclusion loop.

By just saying `struct Doer;` we mean "somewhere there is a Doer. Don't worry, the linker will find it where it is".

Back to the project...
```c++
#pragma once
#include "IElement.hpp"

#include <iostream>

struct Doer : IElement
{
	void accept(IVisitor *v)
	{
		v->visit(this);
	}

	void doSomething()
	{
		std::cout << "[DOER] Doing something" << std::endl;
	}
};
```

```c++
#pragma once
#include "IElement.hpp"

#include <iostream>

struct Thinker : IElement
{
	void accept(IVisitor *v)
	{
		v->visit(this);
	}

	void thinkAbout(std::string thought)
	{
		std::cout << "[Thinker] Thinking about ";
		std::cout << thought << std::endl;
	}
};
```

```c++
#pragma once
#include "IElement.hpp"

#include <iostream>

struct Writer : IElement
{
	void accept(IVisitor *v)
	{
		v->visit(this);
	}

	void WriteSomething()
	{
		std::cout << "[WRITER] Blah, blah, blah... something";
		std::cout << std::endl;
	}
};
```

Notice, that in each element we accept the visitor and point to the accepting object. We don't know **what**, **how**, or **when** the visitor will be doing anything to that particular object. We just accept it.
It is up to the visitor what to do.
```c++
#pragma once
#include "Doer.hpp"
#include "Thinker.hpp"
#include "Writer.hpp"

struct GuestVisitor : IVisitor
{
	void visit(Doer *d)
	{
		d->doSomething();
	}
	void visit(Thinker *t)
	{
		t->thinkAbout("the GuestVisitor");
	}
	void visit(Writer *w)
	{
		w->WriteSomething();
	}
};
``` 
The `GuestVisitor` is quite simple. Depending on the type of object it visits, it performs different tasks.
In the `main` function we can see the beauty of the solution:
```c++
#include <iostream>
#include "List.hpp"
#include "Doer.hpp"
#include "Thinker.hpp"
#include "Writer.hpp"
#include "GuestVisitor.hpp"

int main()
{
	std::cout << "Hello in the visitor pattern example in C++";
	std::cout << std::endl;

	List<IElement *> objects(2);

	objects.add(new Doer);
	objects.add(new Thinker);
	objects.add(new Writer);
	objects.add(new Doer);

	auto visitor = new GuestVisitor;

	std::cout << "The GuestVisitor will now visit each object";
	std::cout << std::endl;

	for (auto object : objects)
		object->accept(visitor);
}
```

We create a list of objects. The only commonality is the implementation of the `IElement`. Next we create some Visitor and visit each element in a loop fashion.

# Conclusion
The Visitor DP gives us two things:

1. Releases us from the hustle of complicated cross-object method invocation. From now on, there is a visitor, who curates the visiting process,
2. Gathers collective object control into a specific location. What would normally be done by a pile of `switch` cases raging all around the project, now is one, two classes with defined scope and purpose.

# Ways of expansion
Go on and experiment with these examples! You can:

* expand each concrete class with other specific methods,
* add another visitor with different `rules of engagement`.