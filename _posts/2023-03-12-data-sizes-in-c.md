---
layout: post
title: "Data sizes in C"
date: 2023-03-12 07:00:00 -0000
categories: c
---
In a strongly-typed language family of `C`, the Coder need to know precisely,
how much space her/his data actually occupy in the memory. This is especially important in 
the embedded world, where the Coder needs to fit data into physical extensions of the CPU.

## What are we used to?
We are used to (GCC x64):
```c
sizeof(char);          // 1
sizeof(short);         // 2
sizeof(int);           // 4
sizeof(long long int); // 8

// and so on...
```

One time, I was compiling a simple code for the Texas Instrumets 
[MSP430 CPU](https://en.wikipedia.org/wiki/TI_MSP430). Its a small, very low power microcontroller. 
I was trying to fit an array of 5000 characters into the RAM, for whatever reason I don't remember
right now. After all, I did have a 4kB RAM version of that CPU. To my misery, I kept receiving 
linking errors, that my data couldn't fit the `.data` section (the stack, basically). Why was that? Yet 
the `char` __HAS__ one byte with... Isn't it?

## What `sizeof` actually shows?
That was the moment, I forgot, that _`sizeof()` doesn't tell the byte size a type_. As the 
[standard says](https://en.wikipedia.org/wiki/Sizeof), `sizeof` expresses data size measured in _char-sized_
storage units. `char` is guaranteed to be `1`. The `char` type is supposed to be the basic data unit, a given
CPU architecture can handle.

Back to my story. The linking failed, because I declared `char t[5000]` in a 4kB RAM system. MSP430 is a
16-bit architecture, where (for power consumption reasons), the basic word you can access is 16-bits.
The `sizeof(char)==1` is true here, but `char` is 16-bits wide. The memory is byte organized. Trying to allocate
`2[B] * 5000 = 10000 [B]` I came 6000 bytes to short (actually more, because of the allocation of other stuff,
but lets keep it simple here).

## What the ISO C standard says?
The ISO C - C99, says that... the precise implementation of `sizeof` is vendor-specific. Each compiler producer,
each IC vendor can determine the behavior of that operator as he sees fit. Well to some extent, actually.
```shell
The sizes of data types:

         NAME    SIZE in bytes
         char >= 1 smallest addressable piece of data
    short int >= 2
          int >= 2
     long int >= 4
long long int >= 8
        float >= 4
	   double >= 8
```

## How to deal with this ambiguity?
One way is to scrap the old `char`, `int`, `float` types. In those places fit the `uint8_t`,
`uint32_t`,  from the `<stdint.h>` (since `C99`). 

The other way is to use the defines in `<stdint.h>` and check in code (at runtime) the sizes 
and limits of primitive data types.

The third way is to define a custom `types.h` header file. There define custom types 
and curate it and customize each time you make a platform switch.

## What about structures and pointers?
The last thing to think about are structures of data and pointers.

Data structures are always some combinations of primitive data types (and/or pointers to those types).
The data structure can be padded or not by the compiler. For example:

```c
// PC x64
typedef struct SomeStruct
{
	char aTable[3];
};

sizeof(SomeStruct); // 4 ???
```

This is because the CPU by default handles in an optimized way data, that are aligned to full 4 bytes.
But, you may argue, that the structure only has a single table, so why pad it with extra (unused) space?
Well:

```c
//PC x64
SomeStruct tableOfStructs[10];

sizeof(tableOfStructs); // 40 !!! (4 * 10)

SomeStruct *ptr = &tableOfStructs[3];
```

Here, the act of assigning the address of an array element is the interesting one. This is the instruction,
the whole structure is being padded for. What can we do about it? Check your compiler options...

For GCC, for example, you can try to add the `__attribute__ ((packed))` attribute after the structure 
definition. But I may be risky - if someone elses code or (worse) your own from the past does factor
in the padding, now suddenly the new version doesn't pad for this particular structure:

```c
// PC x64
typedef struct SomeStruct
{
	char aTable[3];
}__attribute__ ((packed));

sizeof(SomeStruct); // 3 maybe?
```

Consider the downgrade of performance, if you access thousands of times an big array full of unaligned data.
Maybe that extra wasted byte isn't all that worth it?

As it goes for pointers to stuff it should be rather simple. A pointer is basically an address to a piece of
memory, with additional intelligence, how to move up and down (by how many bytes.../chars/words). This
'intelligence' is embedded into the assembly code under every `=`, `++`, `--` and `[]` operators, so in memory
it is effectively an address. An address is as wide as the address bus of the CPU. So, for x64 architecture
it is 8 bytes. For MSP430 it is 16 bytes, as well as for e.g. 
[AVR 8-bit](https://en.wikipedia.org/wiki/AVR_microcontrollers).

## Conclusion
The data types, data sizes, memory allocation are mostly omitted topics in the development process. When it
comes to memory space optimization, the Coder needs to remember that not every `char` is equal to one byte.