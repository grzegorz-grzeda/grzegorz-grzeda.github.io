---
layout: post
title: "libc - stdbool header"
date: 2023-06-23 07:00:00 -0000
categories: libc
---

Definition of `bool` type in C. Platform dependant implementation, but has to
allow standard logical operations in C to carry on. 
Note: according to ANSI C standard logical operators result in `(int) 0` or `(int) 1` values.

```c
#include <stdbool.h>
```

# Defined types

`bool` as the type to process boolean operations.  

# Macro definitions

`true` as `1`.

`false` as `0`.

`__bool_true_false_are_defined` as `1` - indicates that the boolean foundation is present.

# Possible implementation

```c
typedef int bool;
#define true 1
#define false 0

#define __bool_true_false_are_defined 1
```