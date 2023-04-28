---
layout: post
title: "JS map() and filter()"
date: 2023-05-30 07:00:00 -0000
categories: js
---
When dealing with JS objects collected in arrays, we often have to perform some operations 
on the internals of those objects. 

Lets consider two arrays:
```js
let a=[1, 2, 3, 4, 5, 6];
let b=[];
```

We want the `b` array to be filled with squares of values in `a` array. Usually we do it like this:

```javascript

for(let i = 0; i < a.length; i++)
	b.push(a[i] * a[i]);

// b = [1, 4, 9, 16, 25, 64];
```

This has it's drawbacks:

* the `for` notation is long,
* using many `i` indexes is error-prone-approach,
* statefull - the `i` changes state every iteration.

## Mapping
There is a better way:
```javascript
a.map((el) => 
	b.push(el * el)
);
```

Here, the notation:

* is short,
* no indexing needed - no indexes indeed,
* stateless - the `push` method actually changed state of `b`, but without our explicit 
action. We just call some method with a constant parameter.

But, what with the indexing if you need one? Well, the `map` method tries to invoke the 
code in this way:
```js
array.map(function(currentValue, index, array), thisValue);
```

* the `map` assumes, you provide a method to map on the values
	* `currentValue` is the current element in the mapping process,
	* `index` is the current index (but optional),
	* `array` is the reference to the array, upon which the mapping is invoked (but optional),
* `thisValue` is the optional `this` we can substantiate.

The important fact is, that the `map` method returns a new array with all the results of the
supplied function on the mapped values. In our example, we got a new array of 6 `null`s.

## Filtering
The same goes for the `filter` method. As you might think, this method is usually used to filter
out some values.

The method has the same way of handling that `map` does. The difference is that the `function` the 
`filter` tries to invoke, should return a boolean value. The `filter` returns a new array of values, 
which passed (`true`) the test implemented in the `function`.

Let's print every even number from our `a` array:
```js
let result = a.filter((el) => {
	return ((el % 2) == 0);
})

console.log(result);
// [2, 4, 6]
```

There are more utility methods for arrays in JS. Less code - less mess.