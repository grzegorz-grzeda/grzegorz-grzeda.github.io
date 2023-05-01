---
layout: post
title: "Design patterns intro"
date: 2023-04-27 07:00:00 -0000
categories: ["design patterns"]
---

What binds a society of professionals, are:

* line of conduct,
* common principles,
* pattern for ways to behave, create,
* legal bodies recognizing members of that society,
* ...

We, the Coders share at least the first three of those commonalities. There is The Scribes Oath. There are the
SOLID principles, KISS, and there are Design Patterns. 

## What are Design Patterns?
Design Patterns (DPs)are general, repeatable solutions to commonly occurring problems in software design.
This means, that DPs don't deliver precise answers to certain problems, but give hints and clues in which
way direct the workload to achieve optimal or near-optimal results.

The [origin](https://en.wikipedia.org/wiki/Design_Patterns) of `Design Patterns` can be traced to 
the Gang of Four (GoF):

* Erich Gamma
* Richard Helm
* Ralph Jonson
* John Vlissides

In 1994, they published a book `Design Patterns: Elements of Reusable Object-Oriented Software`. They were the
first to collect common ideas about writing software properly. Although, the first traces of interest in 
software architecture dates way back to the late '70 and '80.

## IRL example
Lets discuss an example from IRL architecture. If there is a road which needs to go across a e.g. river, we
instinctively know, that we need a bridge to provide certain features to this project. We need:

* pillars, 
* spans,
* some kind of road suspension. 
* the land to be modeled around the terminals of the bridge in a way, so that driving through will be 
smooth and seamless.

That was the __general idea__. We don't know:

* what precise type of the bridge we need?
* how wide is the river?
* what kind of transport will move through the bridge (cars, trains, walkers?)? 
* what weather conditions are during each season? 
* what budget do we have? 
* ...

Those were __specific requirements__ we don't know (yet) the answers to. Although we know, that at the end, 
there is a bridge to build.

## Why use them?
DPs are handy, because they answer questions on how to arrange the:

* behavior,
* internals,
* communication between,
* hierarchy

of classes and object in certain situations.

## Why not use them?
When DPs were cataloged, in 1994, some saw them as 'workarounds for missing features in C++'. Especially, functional languages (Lisp, Clojure) have the `design pattern spirit` embedded in them.

Also using DPs for sake of DPs is pointless. Your application stops delivering use cases and starts
delivering DPs to the world. It may be a nice picture, but useless in the end.

Blindly following DPs in each and every spot of the project may lead to huge inefficiencies in the 
way the code handles e.g. big amounts of data. Maybe a piece of assembly macros could ruin the DP structure,
but sure may get the job done.

## Types of patterns
That said, now we can discuss three canonical types of Design Patterns

1. `Creational` - efficient class and object instantiation
	1. [`Abstract Factory`](/design%20patterns/2023/04/28/abstract-factory-pattern.html) - create whole families of similar classes

	2. `Builder` - different ways to create whole packs of similar objects

	3. `Factory Method` - creates instances of derived classes

	5. `Prototype` - ready to clone or copy instance
	
	6. `Singleton` - only single instance of a class if allowed

2. `Structural` - class and object composition. Implementing and containing relations

	1. `Adapter` - Match objects by common interfaces

	2. `Bridge` - decouple representation from implementation by an abstraction

	3. `Composite` - embeds a collection of objects seen as a single object of the same type

	4. `Decorator` - on runtime adds/overrides objects functionality

	5. `Facade` - a simple interface in front of a complicated structure

	6. `Flyweight` - optimized creation and handling of a swarm of objects

	7. `Proxy` - Buffers real objects of the rest of system. Can on runtime change the buffered 
	object

3. `Behavioral` - enforcing communication between objects of certain classes

	1. `Chain of responsibility` - puts different actions in a collection and in a processing object

	2. `Command` - encapsulates different actions behind a common interface

	3. `Interpreter` - natural-like language parser

	4. `Iterator` - access sequentially objects of a collection without collection exposition

	5. `Mediator` - a third party handling interactions between loosely coupled objects

	6. `Memento` - undo functionality

	7. `Observer` - publish/subscribe structure, with subscribers able to react on events

	8. `State` - captures the changes of the system in time. Module state store

	9. `Strategy` - runtime swap of algorithms thanks to interfaces

	9. `Template method` - abstractly defined algorithm skeleton with parts pushed to a subclass

	10. `Visitor` - single access object handles algorithms over other object collection

4. Additional (not included in the canonical list of Design Patterns):

	1. `Object Pool` (Creational DP) - a pool of reusable components, ready to grab, without
	the hassle of initialization and maintenance
	
	2. [`Null object`](/design/patterns/2023/04/30/null-object-pattern.html) (Behavioral DP) - A fake implementation of an interface, in case when
	the given functionality is not needed

I will discuss each with you in the future. Don't worry if you don't understand everything
right now - you will soon.

The design pattern post series will contain __at least__ C/C++ examples.