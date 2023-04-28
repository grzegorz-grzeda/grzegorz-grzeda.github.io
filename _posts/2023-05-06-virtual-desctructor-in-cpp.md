---
layout: post
title: "Virtual destructor in C++"
date: 2023-05-06 07:00:00 -0000
categories: "c++"
---
You may have noticed, that every time, when an interface is being introduced, there is a virtual destructor declared.

```c++
struct IInterface{
	virtual ~IInterface() {}

	virtual void someMethod() = 0;
};
```

But actually, what for is that destructor need to be `virtual`?

# Begin with the end in memory

When we construct our objects everything is fine. But what if:
```c++
#include <iostream>

struct Base{
	Base(){ std::cout << "Base Ctor" <<std::endl; }
	~Base(){ std::cout << "Base Dtor" <<std::endl; }
};

struct Deriv : Base{
	Deriv(){ std::cout << "Deriv Ctor" <<std::endl; }
	~Deriv(){ std::cout << "Deriv Dtor" <<std::endl; }
};

int main(){
	//...

	Base* d = new Deriv;

	delete d; // deleting by Base type!!!

	//...
}
```
We would get:
```shell
Base Ctor
Deriv Ctor
Base Dtor
```

Where is the `Deriv Dtor`? What if we were supposed to do something important in the destructor? Some cleanup, send a log entry? This is undefined behavior.

But, if we add that `virtual` to the `Base::~Base()` we will get the result as expexted:
```shell
Base Ctor
Deriv Ctor
Deriv Dtor
Base Dtor
```
Now everything is fine. 
So when should we make dtors virtual? Well, when compiling with `-Wall` flag, the compiler will suggest the answer: if at leas one method is virtual (even not purely), we should make the dtor virtual as well.