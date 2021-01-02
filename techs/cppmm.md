# Memory Management of C++ Containers

## malloc, free, new, and delete

`malloc` and `free` are the keywords in C language to allocate and release memory. `new` and `delete` are C++ keywords for object creations and destruction. 

### malloc and free

`void* malloc(int nbytes)` allocate *n* bytes of dynamic memory from heap, or free store. Malloc invokes system call to request memory from Linux kernel, which adopts sophisticated algorithm to management dynamic memory. 



