---
layout: post
title: "JS spread operator"
date: 2023-05-27 07:00:00 -0000
categories: js
---

All these examples can be run on the [ReplIt](https://repl.it/), setting to NodeJS, HTML/CSS/JS or just entering debug mode on your browser. Hit `F12` if you use Google Chrome and select the `Console` tag. For Mozilla Firefox, press `Ctrl`+`Shift`+Z or search for the `Debugger` option under the `Web Developer` menu item.

# Why use it?
Because it's an easy unroll an array (`spread` it across). Spread operator `...` can be used in cases:

* to fill up function call arguments,
* to compose a straight array of objects,
* to destructure an array.

# Function calls
To call a function we usually do:
```js
let a = [1, 2, 3];

function f(x, y, z) { }

f(a[0], a[1], a[2]);
```

Or we can do this:
```js
//...
f(...a);
```

Even better, we can spread out many things:
```js
let x = [1, 4];
let y = [32, 43];

function k(a, b, c, d, e, f){ }

k(1, ...x, 0, ...y);
// k(a = 1, [b, c] = x, d = 0, [e, f] = y);
```

# Fill arrays
If we would like to compose an array using another array, without a loop, its hard:
```js
let a = [1, 2];
let b = [22, a, 4, 3];
// would give b = [22, [1, 2], 4, 3]
```
Using the spread operator:
```js
let a = [1, 2];
let b = [22, ...a, 4, 3];
// would give b = [22, 1, 2, 4, 3] hurray!
// and this
a.push(5);
// will not modify the b

// to push to the end
let c = [0, 0, 3];
c.push(...a);
/// would be c = [0, 0, 3, 1, 2]
```

Also, if you want to make a `copy` on an array (not just copy the reference to an array, but copy it element by element):
```js
let a = [3, 4];
let b = [...a]; // a brand new copy
```

# Destructure data
If you want to access say the beginning of an array, use the spread:
```js
let [h, ...rest] = [1, 2, 3, 4, 5];
// h = 1; 
// rest = [2, 3, 4, 5];
let [...r, tail] = [4, 3, 2, 1];
// won't work! The spread operator need to be last!
```

# Not all gold...
Although the `...` operator is great, it's not perfect. After all, you can use it only on arrays! 
Playing with objects can't work because:
```js
let a = {s: 1, d: 4};

console.log(...a);
// TypeError: Found non-callable @@iterator
// !!!
```