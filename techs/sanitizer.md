# Clang Sanitizer

## AddressSanitizer

### Introduction

AddressSanitizer is a fast memory error detector. It consists of a compiler instrumentation module and a run-time library. The tool can detect the following types of bugs:

- Out-of-bounds accesses to heap, stack and globals
- Use-after-free
- Use-after-return (runtime flag ASAN_OPTIONS=detect_stack_use_after_return=1)
- Use-after-scope (clang flag -fsanitize-address-use-after-scope)
- Double-free, invalid free
- Memory leaks (experimental)

Typical slowdown introduced by AddressSanitizer is 2x.

### How to build

Build LLVM/Clang with CMake.

### Usage

Simply compile and link your program with `-fsanitize=address` flag. The AddressSanitizer run-time library should be linked to the final executable, so make sure to use clang (not ld) for the final link step. When linking shared libraries, the AddressSanitizer run-time is not linked, so `-Wl,-z,defs` may cause link errors (don't use it with AddressSanitizer). To get a reasonable performance add -O1 or higher. To get nicer stack traces in error messages add `-fno-omit-frame-pointer`. To get perfect stack traces you may need to disable inlining (just use -O1) and tail call elimination (`-fno-optimize-sibling-calls`).








