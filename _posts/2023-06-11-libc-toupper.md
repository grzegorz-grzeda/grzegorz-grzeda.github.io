---
layout: post
title: "libc - toupper()"
date: 2023-06-11 07:00:00 -0000
categories: libc
---
The `toupper()` function is a standard library function in the C programming language. It is defined in the `ctype.h` header file and is used to convert lowercase letters to uppercase letters in a string.

The basic syntax of the `toupper()` function is:

`int toupper(int c);`

Here, the parameter c is an integer that represents the character to be converted. The function returns the uppercase version of the input character if it is a lowercase letter. If the input character is already uppercase or not a lowercase letter, the function simply returns the input character.

The `toupper()` function is often used in applications that involve string manipulation, such as text processing, data cleaning, and data validation. It is also useful for case-insensitive searches and comparisons.

Let's take a look at a few examples of how the `toupper()` function can be used:

Example 1: Converting a string to uppercase

In this example, we use the `toupper()` function to convert a string to uppercase. We create a string and then iterate through each character in the string, converting each lowercase letter to uppercase using the `toupper()` function.

```c
#include <ctype.h>
#include <stdio.h>

int main() {
    char str[] = "this is a test string";
    int i;

    for (i = 0; str[i] != '\0'; i++) {
        str[i] = toupper(str[i]);
    }

    printf("%s\n", str);

    return 0;
}
```

Output:
```
THIS IS A TEST STRING
```

Example 2: Comparing strings without regard to case

In this example, we use the `toupper()` function to compare two strings without regard to case. We first convert both strings to uppercase using the `toupper()` function and then use the strcmp() function to compare the uppercase strings.

```c
#include <ctype.h>
#include <stdio.h>
#include <string.h>

int main() {
    char str1[] = "This is a test string";
    char str2[] = "THIS IS A TEST STRING";
    int i;

    for (i = 0; str1[i] != '\0'; i++) {
        str1[i] = toupper(str1[i]);
    }

    for (i = 0; str2[i] != '\0'; i++) {
        str2[i] = toupper(str2[i]);
    }

    if (strcmp(str1, str2) == 0) {
        printf("The two strings are equal.\n");
    } else {
        printf("The two strings are not equal.\n");
    }

    return 0;
}
```

Output:
```
The two strings are equal.
```

The `toupper()` function is a simple yet powerful tool that is essential for many applications that involve string manipulation. It is important to note that the `toupper()` function only converts lowercase letters to uppercase and does not affect other characters. Additionally, the function does not modify the input character if it is already uppercase or not a lowercase letter.