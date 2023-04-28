---
layout: post
title: "libc - strlen()"
date: 2023-06-14 07:00:00 -0000
categories: libc
---
The `strlen()` function in C is used to find the length of a string. It is defined in the `string.h` header file and takes a string as input. The function returns an integer that represents the number of characters in the string, excluding the null terminating character '\0'.

The syntax of the `strlen()` function is:

`size_t strlen(const char *str);`

Here, `const char *str` is a pointer to the string whose length is to be found. The `size_t` data type is an unsigned integer that is used to represent the size of an object.

The `strlen()` function works by iterating through the characters in the string until it reaches the null terminating character '\0'. The function then returns the number of characters that were counted.

Here is an example of how the `strlen()` function can be used:

```c
#include <stdio.h>
#include <string.h>

int main() {
    char str[] = "Hello, World!";
    int length = strlen(str);

    printf("The length of the string '%s' is %d.\n", str, length);

    return 0;
}
```

Output:
```
The length of the string 'Hello, World!' is 13.
```

In this example, we declare a character array `str` and assign it the value "Hello, World!". We then call the `strlen()` function to find the length of the string and assign the result to an integer variable `length`. Finally, we print the value of `length` using the printf function.

It is important to note that the `strlen()` function does not count the null terminating character '\0' in the string. Therefore, the length of a string that consists only of the null terminating character is 0.

Also, the `strlen()` function assumes that the input string is null-terminated. If the input string is not null-terminated, the behavior of the `strlen()` function is undefined and may result in a runtime error.

In conclusion, the `strlen()` function is a simple but essential function for working with strings in C. It allows us to easily find the length of a string, which is often necessary for various string operations.