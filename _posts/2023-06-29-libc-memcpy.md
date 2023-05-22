---
layout: post
title: "libc - memcpy() function"
date: 2023-06-29 07:00:00 -0000
categories: libc
---
The `memcpy` function in C, is used to copy a block of memory from one buffer to another. Here's a detailed explanation of the code and its functionality:

The `memcpy` function takes three parameters:

1. `dst`: A pointer to the destination buffer where the data will be copied. It must be able to accommodate the content of size `num`.

2. `src`: A pointer to the source buffer from where the data will be copied. It is marked as `const` since the function guarantees not to modify the source buffer.

3. `num`: The size (in bytes) of the source buffer, indicating how many bytes should be copied from the source to the destination.

The return value of `memcpy` is the pointer to the destination buffer (`dst`).

The implementation of `memcpy` provided in the code snippet iterates over each byte of the source buffer and assigns it to the corresponding byte in the destination buffer using a `for` loop. It treats the `dst` and `src` pointers as arrays of `unsigned char` (1-byte) elements, allowing individual byte-level assignments.

# Example
```c
#include <string.h> // memcpy

int main(){
    const char *buffer_src = "test_message";
    char buffer_dst[6];

    memcpy(buffer_dst, buffer_src, 4);
    // buffer_dst now contains 't', 'e', 's', 't'
    // rest is garbage

    return 0;
}
```

Here's a step-by-step breakdown of the implementation:

1. The `dst` and `src` pointers are cast to `unsigned char*` to ensure byte-level access during the copying process.

2. The `for` loop iterates `num` times, copying one byte at a time from the source buffer to the destination buffer. This loop copies the entire block of memory, byte by byte.

3. Each byte in the source buffer (`mem_src[i]`) is assigned to the corresponding byte in the destination buffer (`mem_dst[i]`).

4. Finally, the function returns the pointer to the destination buffer (`dst`).

In the provided example, the `memcpy` function is used to copy the first four bytes from the `buffer_src` string ("test_message") to the `buffer_dst` array, which has a size of 6 bytes. After the `memcpy` operation, `buffer_dst` contains the characters 't', 'e', 's', 't', and the rest of the array remains uninitialized (garbage values).

Please note that in real-world scenarios, it's generally advisable to use the standard library function `memcpy` available in `<string.h>` rather than implementing your own version, as the standard library functions are well-tested and optimized for efficiency.

# Possible implementation

```c
void *memcpy (void *dst, const void *src, size_t num)
{
    unsigned char *mem_dst = (unsigned char *) dst;
    unsigned char *mem_src = (unsigned char *) src;
    
    for(size_t i = 0; i < num; i++) {
        mem_dst[i] = mem_src[i];
    }

    return dst;
}
```