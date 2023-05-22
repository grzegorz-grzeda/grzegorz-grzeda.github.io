---
layout: post
title: "libc - stddef header"
date: 2023-06-20 07:00:00 -0000
categories: libc
---

Definition several implicit types used by the C language in expressions, etc.

```c
#include <stddef.h>
```

# Defined types

`ptrdiff_t` result of pointer substraction. The resulting difference is valid only in respect to
two pointers of elements of the same array.

`size_t` Unsigned integral size. Naturally returned by the `sizeof` operator.

`wchar_t` Wide character type.

# Macro functions

`offsetof` Returns member offset of a structure or union type.

# Macro definitions

`NULL` Null pointer