---
layout: post
title: "libc - memset() function"
date: 2023-07-01 07:00:00 -0000
categories: libc
---
The `memset()` function in C, is used to set a block of memory to a specific value. Here's a detailed explanation of the code and its functionality:

The `memset` function takes three parameters:

1. `ptr`: A pointer to the buffer that needs to be set to a specific value. It must be able to accommodate the content of size `num`.

2. `value`: The value to be set in each byte of the buffer. Usually, this value is internally converted to an `unsigned char`.

3. `num`: The size (in bytes) of the buffer, indicating how many bytes should be set to the specified value.

The return value of `memset` is the pointer to the buffer (`ptr`).

# Example
```c
#include <string.h> // memset

int main(){
    char buffer[100] = "test_message";

    memset(buffer, 0, 100);
    // buffer contains all zeros

    return 0;
}
```

The implementation of `memset` provided in the code snippet sets each byte in the buffer to the specified `value`. Here's a step-by-step breakdown of the implementation:

1. The `ptr` pointer is cast to `unsigned char*` to ensure byte-level access during the setting process.

2. The `for` loop iterates `num` times, setting one byte at a time in the buffer. This loop sets the entire block of memory, byte by byte.

3. Each byte in the buffer is assigned the specified `value`.

4. Finally, the function returns the pointer to the buffer (`ptr`).

In the provided example, the `memset` function is used to set all 100 bytes of the `buffer` array to the value of `0`. After the `memset` operation, the `buffer` contains all zeros.

Please note that, similar to `memcpy`, in real-world scenarios, it's generally advisable to use the standard library function `memset` available in `<string.h>` rather than implementing your own version, as the standard library functions are well-tested and optimized for efficiency.

# Possible implementation
```c
void *memset(void *ptr, int value, size_t num) {
    for (size_t i = 0; i < num; i++) {
        *(char*)ptr = (char) value;
    }
    return ptr;
}
```