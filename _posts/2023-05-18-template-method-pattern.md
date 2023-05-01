---
layout: post
title: "Template Method design pattern"
date: 2023-05-18 07:00:00 -0000
categories: ["design patterns"]
---

Suppose that you want to  create an architecture for your simple computing software. 
For each case you have to input some integers and perform some calculations.

Use case:
The user inputs some integers, and as a result, the software computes variations of 
calculus computations (all of the numbers are summed up to a single result value).
Depending on the algorithm, each number may be prepared for calculations, as well
as the end result.

This means, that for every algorithm there is the 'summing' part, common for every one
of them. We know this - we can delegate this functionality to a super class. 
The second part is the computation structure. First we have to condition the input values.
After the summing we have to condition the end result.

Those two conditioning processes are different for each algorithm. They will be implemented
in the subclasses.

## Example project
This project can be found on my [GitHub page](https://github.com/grzegorz-grzeda/template-method-pattern-example-cpp). It has the following structure:
```shell
template-method-pattern-example-cpp \
	main.cpp
	Makefile
	AComputer.hpp
	SumOfSquares.hpp
	RootsSummed.hpp
```

The `main.cpp` and `Makefile` are self explanatory (I hope). If not, you can visit
[my other](/series/patterns/) Design Pattern posts and dive deeper.

The `AComputer` is the abstract superclass, holding the algorithm, memory management and 
all the mechanisms needed to perform the task.
```c++
#ifndef ACOMPUTER_HPP__
#define ACOMPUTER_HPP__

struct AComputer
{
	AComputer(int siz) : size(siz), cnt(0), result(0)
	{
		numbers = new int[siz];
	}
	virtual ~AComputer()
	{
		delete[] numbers;
	}
	void put(int number)
	{
		if (cnt < size)
			numbers[cnt++] = number;
	}
	void compute()
	{
		prepare();
		calculate();
		finalize();
	}
	int getResult()
	{
		return result;
	}

protected:
	int *numbers;
	int size;
	int cnt;
	int result;

	virtual void prepare() = 0;
	virtual void finalize() = 0;

private:
	void calculate()
	{
		result = 0;

		for (int i = 0; i < cnt; i++)
			result += numbers[i];
	}
};

#endif
```
Here you see the `prepare()` and `finalize()` **template methods** to be implemented
in the subclasses, holding specific details about the algorithms.

There are two: `SumOfSquares` and `RootsSummed`. The first one:
```c++
#ifndef SUMOFSQUARES_HPP__
#define SUMOFSQUARES_HPP__

#include "AComputer.hpp"

struct SumOfSquares : AComputer
{
	SumOfSquares(int size) : AComputer(size) {}

private:
	void prepare()
	{
		for (int i = 0; i < cnt; i++)
			numbers[i] *= numbers[i];
	}
	void finalize()
	{
	}
};

#endif // !SUMOFSQUARES_HPP__
```
Simply squares the input data. The summing is done in the superclass.

The `RootsSummed`:
```c++
#ifndef ROOTSSUMMED_HPP__
#define ROOTSSUMMED_HPP__

#include "AComputer.hpp"
#include <cmath>

struct RootsSummed : AComputer
{
	RootsSummed(int size) : AComputer(size) {}

private:
	void prepare()
	{
		for (auto i = 0; i < cnt; i++)
			numbers[i] = static_cast<int>(std::sqrt(numbers[i]));
	}
	void finalize()
	{
		result = static_cast<int>(sqrt(result));
	}
};

#endif // !ROOTSSUMMED_HPP
```
Takes the square root of each input value, gets summed in the superclass and takes
the square root of the result. Just a fancy algorithm.

Finally, the `main.cpp`:
```c++
#include <iostream>
#include <cstdlib>
#include "SumOfSquares.hpp"
#include "RootsSummed.hpp"

int main(int argc, char *argv[])
{
	if (argc < 2)
		return -1;

	auto size = argc - 1;
	auto nums = new int[size];

	for (auto i = 0; i < size; i++)
		nums[i] = std::atoi(argv[i + 1]);

	AComputer *sosq = new SumOfSquares(size);
	AComputer *rtsm = new RootsSummed(size);

	for (auto i = 0; i < size; i++)
	{
		sosq->put(nums[i]);
		rtsm->put(nums[i]);
	}

	sosq->compute();
	rtsm->compute();

	std::cout << "SumOfSquares: " << sosq->getResult() << std::endl;
	std::cout << " RootsSummed: " << rtsm->getResult() << std::endl;

	delete rtsm;
	delete sosq;

	return 0;
}
```

Here the program takes the input values (if supplied), initiates the two algorithms
and performs computations. Lastly, the results are printed. Notice, that `sosq` and
`rtsm` are both instances of `AComputer`. We have access only to `put(int)`,
`compute()` and `getResults()`. The specialized methods `prepare()` and `finalize()`
are obscured. The `AComputer` handles all the interactions and sequencing needed.

The example run:
```shell
>template-method-pattern-example-cpp.exe 1 2 3 4 5
SumOfSquares: 55
 RootsSummed: 2
```

You can see, that the Template Method is actually the essence of inheritance. But, here it
is put to work in a specific way. The methods need to be called in the superclass in the same
order each time - the algorithm method. The subclasses just provide the lacking functionality,
the superclass didn't have. This scaffold lets us create as many algorithms as needed. We could
place the algorithms in a collection and perform computation sequentially.

## Benefits
We get:

* Simple architecture based on inheritance,
* Grouping of the sequence and data handling in the super class,
* When using delegation instead of inheritance, we get the **Strategy Design Pattern**, where
we can replace whole algorithms.

## Traps
We have to watch out:

* we have to set the abstract methods as protected, so that no one outside of the
algorithm would access the without order and care of the superclass,
* When returning object implementing interfaces from subclass method we actually use 
**Factory Method** instead of **Template Method**.

## Conclusion
Don't underestimate the power of the Template Method Design Pattern! Such a simple solution may 
save you from the hassle of dealing with many detached classes, with undisclosed similarities.