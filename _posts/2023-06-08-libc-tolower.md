---
layout: post
title: "libc - tolower()"
date: 2023-06-08 07:00:00 -0000
categories: libc
---
The `tolower()` function is a standard library function in the C programming language. It is defined in the `ctype.h` header file and is used to convert uppercase letters to lowercase letters in a string.

The basic syntax of the `tolower()` function is:

`int tolower(int c);`

Here, the parameter c is an integer that represents the character to be converted. The function returns the lowercase version of the input character if it is an uppercase letter. If the input character is already lowercase or not an uppercase letter, the function simply returns the input character.

The `tolower()` function is often used in applications that involve string manipulation, such as text processing, data cleaning, and data validation. It is also useful for case-insensitive searches and comparisons.

Let's take a look at a few examples of how the `tolower()` function can be used:

Example 1: Converting a string to lowercase

In this example, we use the `tolower()` function to convert a string to lowercase. We create a string and then iterate through each character in the string, converting each uppercase letter to lowercase using the `tolower()` function.

```c
#include <ctype.h>
#include <stdio.h>

int main() {
    char str[] = "THIS IS A TEST STRING";
    int i;

    for (i = 0; str[i] != '\0'; i++) {
        str[i] = tolower(str[i]);
    }

    printf("%s\n", str);

    return 0;
}
```

Output:
```
this is a test string
```

Example 2: Comparing strings without regard to case

In this example, we use the `tolower()` function to compare two strings without regard to case. We first convert both strings to lowercase using the `tolower()` function and then use the strcmp() function to compare the lowercase strings.

```c
#include <ctype.h>
#include <stdio.h>
#include <string.h>

int main() {
    char str1[] = "This is a test string";
    char str2[] = "this is a test string";
    int i;

    for (i = 0; str1[i] != '\0'; i++) {
        str1[i] = tolower(str1[i]);
    }

    for (i = 0; str2[i] != '\0'; i++) {
        str2[i] = tolower(str2[i]);
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

The `tolower()` function is a simple yet powerful tool that is essential for many applications that involve string manipulation. It is important to note that the `tolower()` function only converts uppercase letters to lowercase and does not affect other characters. Additionally, the function does not modify the input character if it is already lowercase or not an uppercase letter.