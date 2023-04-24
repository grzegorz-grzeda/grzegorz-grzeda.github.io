---
layout: post
title: "Hello world"
date: 2023-03-15 07:00:00 -0000
categories: general
---
There is a set of principles how to organize code in a project. It's more a moral conduct than a recipe. They were introduced by Robert Martin in the year 2000. These principles are platform/language agnostic, so they apply anywhere, where there is code.

Solid Principles are more abstract than [Design Patterns](/posts/design-patterns-intro/) and operate in another dimension of coding. In a place, where The Coder retrospects the code and applies architectural changes. 

Here we will touch very briefly on each principle and expand them in separate posts.

# The principles
Each letter in the `SOLID` acronym stands for a separate `quality` in a code project:

1. **SRP** - Single Responsibility Principle

A class/structure (module) should have one reason to change. Two and more reasons mean, that there sholud be two or more classes with some relation between them.

2. **OCP** - Open/Close Principle

A class/module should be open to extension and close to modification. How to extend without modifying? Stay tunned...

3. **LSP** - Liskov Substitution Principle

All subclasses should behave in a way like their superclasses. There should not be any implicit 'magic' function call sequence overriding the inherited rules. If so - there should not be any inheritance in the first place.

4. **ISP** - Interface Segregation Principle

While designing interfaces, they should demand implementing only substantial behaviors. If a class has to implement a method that will be left empty it creates a lie. That class shouldn't implement that interface, or the method in question should not be in that interface.

5. **DIP** - Dependency Inversion Principle

The high level business rules should not depend on low level details. The path of `#include...` will show the way what depends on what. A way to inverse the dependency path is by introducing interfaces and injecting relations through a common injecting place.

# Conclusion
This all looks as `common sense`, but is usually hard to implement and sustain in the long run. We will investigate ways of implementing these principles and keeping them in check.