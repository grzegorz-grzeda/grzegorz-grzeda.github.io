---
layout: post
title: "libc - limits header"
date: 2023-06-26 07:00:00 -0000
categories: libc
---

Definition of limits of standard data types.

Limits for floating-point types are defined in `<float.h>`.
Limits for width-specific integral types and other typedefs are defined in `<stdint.h>`.

```c
#include <limits.h>
```

# Macro definitions

Please note - all these definitions are implementation/platform dependent. These values are true for `x32/x64` platform.

|name|expression|possible value|
|---|---|---|
|`CHAR_BIT`|Number of bits in a char object (byte)|`8` or greater|
|`SCHAR_MIN`|Minimum value for an object of type signed char|`-127` (-2<sup>7</sup> + 1) or less|
|`SCHAR_MAX`|Maximum value for an object of type signed char|`127` (2<sup>7</sup> - 1) or greater|
|`UCHAR_MAX`|Maximum value for an object of type unsigned char|`255` (2<sup>8</sup> - 1) or greater|
|`CHAR_MIN`|Minimum value for an object of type char|either `SCHAR_MIN` or `0`|
|`CHAR_MAX`|Maximum value for an object of type char|either `SCHAR_MAX` or `UCHAR_MAX`|
|`MB_LEN_MAX`|Maximum number of bytes in a multibyte character, for any locale|`1` or greater|
|`SHRT_MIN`|Minimum value for an object of type short int|`-32767` (-2<sup>15</sup> + 1) or less|
|`SHRT_MAX`|Maximum value for an object of type short int|`32767` (2<sup>15</sup> - 1) or greater|
|`USHRT_MAX`|Maximum value for an object of type unsigned short int|`65535` (2<sup>16</sup> - 1) or greater|
|`INT_MIN`|Minimum value for an object of type int|`-32767` (-2<sup>15</sup> + 1) or less|
|`INT_MAX`|Maximum value for an object of type int|`32767` (2<sup>15</sup> - 1) or greater|
|`UINT_MAX`|Maximum value for an object of type unsigned int|`65535` (2<sup>16</sup> - 1) or greater|
|`LONG_MIN`|Minimum value for an object of type long int|`-2147483647` (-2<sup>31</sup> + 1) or less|
|`LONG_MAX`|Maximum value for an object of type long int|`2147483647` (2<sup>31</sup> - 1) or greater|
|`ULONG_MAX`|Maximum value for an object of type unsigned long int|`4294967295` (2<sup>32</sup> - 1) or greater|
|`LLONG_MIN`|Minimum value for an object of type long long int|`-9223372036854775807` (-2<sup>63</sup> + 1) or less|
|`LLONG_MAX`|Maximum value for an object of type long long int|`9223372036854775807` (2<sup>63</sup> - 1) or greater|
|`ULLONG_MAX`|Maximum value for an object of type unsigned long long int|`18446744073709551615` (2<sup>64</sup> - 1) or greater|