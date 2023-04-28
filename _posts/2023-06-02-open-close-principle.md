---
layout: post
title: "Open/Close principle"
date: 2023-06-02 07:00:00 -0000
categories: solid
---

In software architecture its all about happy clients. Happy clients gets from working software and well delivered requirements. When clients change their minds at the end its up to us, The Coders, to how we respond to those changes. 

A good piece of software is such that is easy to change. When requirements update, so should the architecture and every module.

Adhering to the **Open-Closed Principle** provides such 'plasticity'. The piece of software is at the same time:

* open for extension,
* closed for modification.

## Closed to modification
By defining functionality of a module, we tell the world what it is capable of *doing*.
Let's consider a [CHIP8 emulator](https://en.wikipedia.org/wiki/CHIP-8) to be written in C++. We can create an interface for every command to be executed:

```c++
// the Context is defined somewhere else

struct ICommand{
	virtual ~ICommand(){}

	virtual void execute(Context &) = 0;
};
```

Every command, while loaded is executed in the same way. There is no way a command would be treated in another way.

## Open to extension
Most of commands advance the `Program Counter` by two bytes:

```c++
struct ANormalCommand : ICommand{
	virtual ~ANormalCommand(){}

	virtual void performOperation(Context &) = 0;

	void execute(Context &context){
		performOperation(context);

		advancePc(context);
	}

private:
	void advancePc(Context &context){
		context.pc += 2;
	}
};
```

Also, lets consider, that we want to add a logging feature to each command. We could do that on many different ways - by an [template method](/posts/template-method-pattern/), by a [visitor](/posts/visitor-design-pattern/) or [observer](/posts/observer-pattern/) Design Pattern.

We may now **extend** the functionality of the `ANormalCommand`:

```c++
// Logger defined elsewhere

struct ANormalCommand : ICommand{
	// ...
	ANormalCommand(Logger& l):
		l(l)
	{
	}

	void execute(Context &context){
		l.i(name());

		performOperation(context);

		advancePc(context);
	}

	virtual std::string name() = 0;
private:
	Logger &l;
};
```

## Summary
The `OCP` is a guard, reminding us, that software architecture is about proper decoupling of modules. Besides stiff and rigid relations, the software also should be easy to modify/update. Enabling openness to extension and closeness for modification we achieve both: sustaining current architecture and extending modules for new functionality.