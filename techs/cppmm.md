# Memory Management of C++ Containers

## malloc, free, new, and delete

`malloc` and `free` are the keywords in C language to allocate and release memory. `new` and `delete` are C++ keywords for object creations and destruction. 

### malloc and free

`void* malloc(int nbytes)` allocate *n* bytes of dynamic memory from heap, or free store. Malloc invokes system call to request memory from Linux kernel, which adopts sophisticated algorithm to management dynamic memory. `free(p)` releases the memory allocated at the pointer `p`, and `p` should be returned from a successful `malloc` call.

The Linux kernel maintains the number of bytes allocated by `malloc` using additional memory area called cookie, such that the `free` operation could release the correct length of memory. Some memory allocation algorithms are designed without using cookie in order to save memory usage.

### new and delete

`new` operation constructs an object on the free store. 

