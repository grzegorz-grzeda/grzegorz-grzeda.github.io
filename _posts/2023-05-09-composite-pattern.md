---
layout: post
title: "Composite design pattern"
date: 2023-05-09 07:00:00 -0000
categories: "design patterns"
---
In applications, we have a desire to aggregate objects into collections. This way we have a coherent container where we can organize those objects. But when it comes to easy access to those objects we often have to write query mechanisms. What if there would be a way to write the container in a way so that i behaves just like the objects it holds?

# Project
We will write a simple application (all of the code available on [my GitHub](https://github.com/grzegorz-grzeda/composite-pattern-example-cpp))that does stuff (or say that it does 😉). We will define an interface `IComponent` that defines all the actions. Let's say, that the `IComponent` is a somewhat a Command DP (because of the executional behavior).

```c++
struct IComponent
{
	virtual ~IComponent(){}

	virtual const char* getName(const char* prefix) = 0;

	virtual void execute() = 0;
};
```

The `getName()` receives the `prefix` parameter is used for the prefixing during listing of the hierarchy of objects under the composite.

Some `IComponent`'s:
```c++
// Doer.hpp
struct Doer : IComponent
{
	Doer(std::string name)
		: name("DOER_" + name)
	{
	}
	const char *getName(const char *prefix = "")
	{
		resultName = (prefix + name);
		return resultName.c_str();
	}
	void execute()
	{
		std::cout << "[" << getName() << "] Doing someting..." << std::endl;
	}

private:
	std::string name;
	std::string resultName;
};
```
```c++
// Commenter.hpp
struct Commenter : IComponent
{
	Commenter(std::string name)
		: name("COMMENTER_" + name)
	{
	}
	const char *getName(const char *prefix)
	{
		resultName = prefix + name;
		return resultName.c_str();
	}
	void execute()
	{
		std::cout << "[" << name << "] I don't know what I'm doing..." << std::endl;
	}

private:
	std::string resultName;
	std::string name;
};
```
These are simple objects describing what they are doing. Nothing fancy.

The `Composite` is a `IComponent` extended with proper aggregation functionality. It a simple wrap around our [`List`](posts/simple-list-for-embedded-c-/) container.

```c++
struct Composite : IComponent
{
	Composite(std::string name)
		: components(2),
		  name("LIST_" + name)
	{
	}

	void add(IComponent *component)
	{
		components.add(component);
	}

	const char *getName(const char *prefix = "")
	{
		resultName = prefix;
		resultName += name;
		
		auto pref = std::string(prefix) + " ";

		for (auto component : components)
		{
			resultName += std::string("\n");
			resultName += prefix;
			resultName += component->getName(pref.c_str());
		}

		return resultName.c_str();
	}

	void execute()
	{
		for (auto component : components)
			component->execute();
	}

private:
	List<IComponent *> components;
	std::string name;
	std::string resultName;
};
```

This is the most complicated `IComponent` part. The `execute()` function simply invokes execution of child components. The `getName()` gets the name of the list component as well as its children. Here the prefix helps in aligning the result string so that we can see the structure.

The `main()`:
```c++
int main(int argc, char *argv[])
{
	auto mainComponent = new Composite("main");
	auto secondaryComposite = new Composite("secondary");
	auto firstDoer = new Doer("1");
	auto secondDoer = new Doer("2");
	auto thirdDoer = new Doer("3");

	auto lastCommenter = new Commenter("nobody");

	mainComponent->add(firstDoer);
	mainComponent->add(secondaryComposite);
	mainComponent->add(lastCommenter);

	secondaryComposite->add(secondDoer);
	secondaryComposite->add(thirdDoer);

	std::cout << "Application structure:" << std::endl;
	std::cout << mainComponent->getName() << std::endl;

	mainComponent->execute();

	return 0;
}
```

The end result:
```shell
$>composite-pattern-example-cpp.exe
Application structure:
LIST_main
 DOER_1
 LIST_secondary
   DOER_2
   DOER_3
 COMMENTER_nobody
[DOER_1] Doing someting...
[DOER_2] Doing someting...
[DOER_3] Doing someting...
[COMMENTER_nobody] I don't know what I'm doing...
```

Notice, that the list component implements the same behavior as the objects it gathers. The access method is unified. No `get()`, no iteration at `main()` level. Simple `getName()` and `execute()` invocations.

# Conclusion
The Composite DP gives us the power to unify the interface of interaction between a class of objects and the container gathering those objects.