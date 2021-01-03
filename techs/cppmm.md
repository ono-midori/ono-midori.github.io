# Memory Management of C++ Containers

## malloc, free, new, and delete

`malloc` and `free` are the keywords in C language to allocate and release memory. `new` and `delete` are C++ keywords for object creation and destruction. 

### malloc and free

`void* malloc(int nbytes)` allocates *n* bytes of dynamic memory from heap, or free store. Malloc invokes system call to request memory from Linux kernel, which adopts sophisticated algorithm to management dynamic memory. `free(p)` releases the memory allocated at the pointer `p`, and `p` should be returned from a successful `malloc` call. The performance of malloc and free is controlled by user.

The Linux kernel maintains the number of bytes allocated by `malloc` using additional memory area called cookie, such that the `free` operation could release the correct length of memory. Some memory allocation algorithms are designed without using cookie in order to save memory usage.

### new and delete

The `new` function call constructs an object on the free store. The function first allocates memory using `malloc`, and the number of bytes can be learned from the sizeof the object type (for non-POD object, how to determine the length of memory allocated?), and then calls the constructor of the object (perhaps by placement-new operation). Array new (`new T[n]`) firstly allocates *n* objects of memory, and then constructs *n* objects at the correct location. The `delete` function call destroys a previously `new`ed object. It first calls the destructor of the object, and then release the memory to kernel. Array delete (`delete[]`) firstly calls the destructor one-by-one, and then release the memory space of all objects back to the kernel. It is a rule that the array delete and the array new must be paired. If an array new is paired with a raw delete, the memory space of *n* object is still returned to the kernel, but only the destructor of the first object is invoked. If all the object is POD type, the destructor is trivial, and the situation is benign. On the other hand, if the operations in the destructor are non-trivial, some resource may be leaked.

### Usage of Imbedded Pointer to Avoid Cookie

