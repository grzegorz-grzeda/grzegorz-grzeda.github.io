---
layout: post
title: "libc - ctype.h functions"
date: 2023-06-17 07:00:00 -0000
categories: libc
---
Here I gathered the return values of `ctype.h` functions:
- ✅ - return value `!= 0` (`true`)
- ❌ - return value `== 0` (`false`)

Every function has the same signature: `int isXXX(char c)`:


|ASCII<br/>values|characters|`iscntrl`|`isprint`|`isspace`|`isblank`|`isgraph`|`ispunct`|`isalnum`|`isalpha`|`isupper`|`islower`|`isdigit`|`isxdigit`|
|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|
|0x00<br/>0x08|NULL, etc.|✅|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|
|0x09|`'\t'`|✅|❌|✅|✅|❌|❌|❌|❌|❌|❌|❌|❌|
|0x0A<br/>0x0D|`'\n'`, `'\r'`, `'\v'`,<br/>etc.|✅|❌|✅|❌|❌|❌|❌|❌|❌|❌|❌|❌|
|0x0E<br/>0x1F|control codes|✅|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|
|0x20|space|❌|✅|✅|✅|❌|❌|❌|❌|❌|❌|❌|❌|
|0x21<br/>0x2F|!"#$%&'()*+,-./|❌|✅|❌|❌|✅|✅|❌|❌|❌|❌|❌|❌|
|0x30<br/>0x39|0123456789|❌|✅|❌|❌|✅|❌|✅|❌|❌|❌|✅|✅|
|0x3A<br/>0x40|:;<=>?@|❌|✅|❌|❌|✅|✅|❌|❌|❌|❌|❌|❌|
|0x41<br/>0x46|ABCDEF|❌|✅|❌|❌|✅|❌|✅|✅|✅|❌|❌|✅|
|0x47<br/>0x5A|GHIJKLMNOP<br/>QRSTUVWXYZ|❌|✅|❌|❌|✅|❌|✅|✅|✅|❌|❌|❌|
|0x5B<br/>0x60|[\]^_`|❌|✅|❌|❌|✅|✅|❌|❌|❌|❌|❌|❌|
|0x61<br/>0x66|abcdef|❌|✅|❌|❌|✅|❌|✅|✅|❌|✅|❌|✅|
|0x67<br/>0x7A|ghijklmnop<br/>qrstuvwxyz|❌|✅|❌|❌|✅|❌|✅|✅|❌|✅|❌|❌|
|0x7B<br/>0x7E|{\|}~|❌|✅|❌|❌|✅|✅|❌|❌|❌|❌|❌|❌|
|0x7f|DEL|✅|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|