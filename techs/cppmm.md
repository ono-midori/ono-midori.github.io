# Memory Management of C++ Containers

[TOC]

## malloc, free, new, and delete

`malloc` and `free` are the keywords in C language to allocate and release memory. `new` and `delete` are C++ keywords for object creation and destruction. 

### malloc and free

`void* malloc(int nbytes)` allocates *n* bytes of dynamic memory from heap, or free store. Malloc invokes system call to request memory from Linux kernel, which adopts sophisticated algorithm to management dynamic memory. `free(p)` releases the memory allocated at the pointer `p`, and `p` should be returned from a successful `malloc` call. The performance of malloc and free is controlled by user.

The Linux kernel maintains the number of bytes allocated by `malloc` using additional memory area called cookie, such that the `free` operation could release the correct length of memory. Some memory allocation algorithms are designed without using cookie in order to save memory usage.

### new and delete

The `new` function call constructs an object on the free store. The function first allocates memory using `malloc`, and the number of bytes can be learned from the sizeof the object type (for non-POD object, how to determine the length of memory allocated?), and then calls the constructor of the object (perhaps by placement-new operation). Array new (`new T[n]`) firstly allocates *n* objects of memory, and then constructs *n* objects at the correct location. The `delete` function call destroys a previously `new`ed object. It first calls the destructor of the object, and then release the memory to kernel. Array delete (`delete[]`) firstly calls the destructor one-by-one, and then release the memory space of all objects back to the kernel. It is a rule that the array delete and the array new must be paired. If an array new is paired with a raw delete, the memory space of *n* object is still returned to the kernel, but only the destructor of the first object is invoked. If all the object is POD type, the destructor is trivial, and the situation is benign. On the other hand, if the operations in the destructor are non-trivial, some resource may be leaked.

### Four Versions of Global New

```c++
/* new_delete2.cpp */
#include <iostream>

using namespace std;

class Bad {};

class Foo {
public:
  Foo() { cout << "Foo::Foo()" << endl; }

  Foo(int) {
    cout << "Foo::Foo(int)" << endl;
    throw Bad();
  }

  void *operator new(size_t size) { // normal version
    cout << "operator new(size_t size), size=" << size << endl;
    return malloc(size);
  }

  void *operator new(size_t size, void *start) { // placement new
    cout << "operator new(size_t size, start), size=" << size
         << ", start=" << start << endl;
    return start;
  }

  // the first parameter must be size_t type
  void *operator new(size_t size, long extra) {
    cout << "operator new(size_t size, long extra), size=" << size
         << ", extra=" << extra << endl;
    return malloc(size + extra);
  }

  void *operator new(size_t size, long extra, char init) {
    cout << "operator new(size_t size, long extra, char init), size=" << size
         << ", extra=" << extra << ", init=" << init << endl;
    return malloc(size + extra);
  }

  // error
  /*
  void* operator new(long extra, char init) {
          return malloc(extra);
  }
  */

  void operator delete(void *, size_t) {
    cout << "operator delete(void*, size_t)" << endl;
  }

  void operator delete(void *, void *) {
    cout << "operator delete(void*, void*)" << endl;
  }

  void operator delete(void *, long) {
    cout << "operator delete(void*, long)" << endl;
  }

  void operator delete(void *, long, char) {
    cout << "operator delete(void*, long, char)" << endl;
  }
};

int main() {
  Foo start;
  cout << "1" << endl;
  Foo *p1 = new Foo; // normal new
  cout << "2" << endl;
  Foo *p2 = new (&start) Foo; // placement new
  cout << "3" << endl;
  Foo *p3 = new (100) Foo; // new(size_t, long)
  cout << "4" << endl;
  Foo *p4 = new (100, 'a') Foo; // new(size_t, long, char)
  cout << "5" << endl;
  Foo *p5 = new (100) Foo(1); // new(size_t, long) with ctor(int)
  cout << "6" << endl;
  Foo *p6 = new (100, 'a') Foo(1); // new(size_t, long, char) with ctor(int)
  cout << "7" << endl;
  Foo *p7 = new (&start) Foo(1); // placement new with ctor(int)
  cout << "8" << endl;
  Foo *p9 = new Foo(1); // ctor(int)
}
```

### Overload New and Delete for Class

The `new` and `delete` operators of class can be overloaded, so that we can customize the memory management operations. For example, this code recipe overload `new`, `delete`, `new []`, and `detele[]` operator for class `Foo`.

```c++
/* new_delete.cpp */
#include <iostream>
#include <string>

using namespace std;

class Foo {
public:
  int _id;
  long _data;
  string _str;

  Foo() : _id(0) {
    cout << "default ctor. this=" << this << " id=" << _id << endl;
  }

  Foo(int i) : _id(i) {
    cout << "ctor. this=" << this << " id=" << _id << endl;
  }

  ~Foo() { // non-trivial dtor.
    cout << "dtor. this=" << this << " id=" << _id << endl;
  }

  static void *operator new(size_t size);
  static void operator delete(void *pdead, size_t size);
  static void *operator new[](size_t size);
  static void operator delete[](void *pdead, size_t size);
};

static void *Foo::operator new(size_t size) {
  Foo *p = (Foo *)malloc(size);
  cout << "operator new, p=" << p << endl;
  return p;
}

static void Foo::operator delete(void *pdead, size_t size) {
  cout << "operate delete, pdead=" << pdead << endl;
  free(pdead);
}

static void *Foo::operator new[](size_t size) {
  Foo *p = (Foo *)malloc(size);
  cout << "operator new[], p=" << p << endl;
  return p;
}

static void Foo::operator delete[](void *pdead, size_t size) {
  cout << "operator delete[], pdead=" << pdead << endl;
  free(pdead);
}
```

First we ignore the customized new and delete operator, and explicitly invoke the global new and delete:

```c++
int main() {
  cout << "sizeof(Foo)=" << sizeof(Foo) << endl;
  Foo *p = ::new Foo(7);
  ::delete p;

  Foo *pArray = ::new Foo[5];
  ::delete[] pArray;
}
```

We get output as follow:

```shell
$ ./new_delete
sizeof(Foo)=48
ctor. this=0x1e53280 id=7
dtor. this=0x1e53280 id=7
default ctor. this=0x1e532c8 id=0
default ctor. this=0x1e532f8 id=0
default ctor. this=0x1e53328 id=0
default ctor. this=0x1e53358 id=0
default ctor. this=0x1e53388 id=0
dtor. this=0x1e53388 id=0
dtor. this=0x1e53358 id=0
dtor. this=0x1e53328 id=0
dtor. this=0x1e532f8 id=0
dtor. this=0x1e532c8 id=0
```

Then we invoke the customized new and delete operation:

```c++
int main() {
  cout << "sizeof(Foo)=" << sizeof(Foo) << endl;
  Foo *p = new Foo(7);
  delete p;

  Foo *pArray = new Foo[5];
  delete[] pArray;
}
```

More information is printed:

```shell
$ ./new_delete
sizeof(Foo)=48
operator new, p=0x194d280
ctor. this=0x194d280 id=7
dtor. this=0x194d280 id=7
operate delete, pdead=0x194d280
operator new[], p=0x194d2c0
default ctor. this=0x194d2c8 id=0
default ctor. this=0x194d2f8 id=0
default ctor. this=0x194d328 id=0
default ctor. this=0x194d358 id=0
default ctor. this=0x194d388 id=0
dtor. this=0x194d388 id=0
dtor. this=0x194d358 id=0
dtor. this=0x194d328 id=0
dtor. this=0x194d2f8 id=0
dtor. this=0x194d2c8 id=0
operator delete[], pdead=0x194d2c0
```

We find that in array new memory layout, there is 8 bytes between the pointers of array and the first object. Why?

### Per-Class Allocator

In the previous example, we directly called `malloc` and `free` in the overridden version of `new` and `delete`. Here we customize the memory allocation procedure:

```C++
/* allocator1.cpp */
#include <cstddef>
#include <iostream>

using namespace std;

class Screen {
public:
  Screen(int x) : i(x) {}
  int get() { return i; }

  void *operator new(size_t);
  void operator delete(void *, size_t);

private:
  Screen *next;
  static Screen *freeStore;
  static const int screenChunk;

  int i;
};

// per class allocator

void *Screen::operator new(size_t size) {
  Screen *p;
  if (!freeStore) {
    size_t chunk = screenChunk * size; // 24 elements
    freeStore = p = reinterpret_cast<Screen *>(new char[chunk]);
    // split the chunk into small pieces and linked them together
    for (; p != &freeStore[screenChunk - 1]; ++p)
      p->next = p + 1;
    p->next = 0;
    cout << "allocate new chunk" << endl;
  }

  p = freeStore;
  freeStore = freeStore->next;
  return p;
}

void Screen::operator delete(void *p, size_t) {
  static_cast<Screen *>(p)->next = freeStore;
  freeStore = static_cast<Screen *>(p);
}

Screen *Screen::freeStore = 0;
const int Screen::screenChunk = 24;

int main() {

  cout << "sizeof(Screen) = " << sizeof(Screen) << endl;

  size_t const N = 100;
  Screen *p[N];
  for (int i = 0; i < N; ++i)
    p[i] = new Screen(i);

  for (int i = 0; i < 10; ++i)
    cout << p[i] << endl;

  for (int i = 0; i < N; ++i)
    delete p[i];
}
```

The `new` operator actually utilizes preallocated chunks of memory, such that creating 100 `Screen` object only invokes 5 times of `malloc` operations.

```shell
$ ./allocator1
sizeof(Screen) = 16
allocate new chunk
allocate new chunk
allocate new chunk
allocate new chunk
allocate new chunk
0xeb3280
0xeb3290
0xeb32a0
0xeb32b0
0xeb32c0
0xeb32d0
0xeb32e0
0xeb32f0
0xeb3300
0xeb3310
```

If we changed the code to use global `new` and `delete` operator:

```c++
int main() {

  cout << "sizeof(Screen) = " << sizeof(Screen) << endl;

  size_t const N = 100;
  Screen *p[N];
  for (int i = 0; i < N; ++i)
    p[i] = ::new Screen(i);

  for (int i = 0; i < 10; ++i)
    cout << p[i] << endl;

  for (int i = 0; i < N; ++i)
    ::delete p[i];
}
```

We find that the addresses between to adjacent objects are increased from 16 to 32. 

```shell
$ ./allocator1
sizeof(Screen) = 16
0x9fc280
0x9fc2a0
0x9fc2c0
0x9fc2e0
0x9fc300
0x9fc320
0x9fc340
0x9fc360
0x9fc380
0x9fc3a0
```

### Usage of Embedded Pointer to Avoid Cookie in Per-Class Allocator

```c++
// allocator2.cpp
#include <iostream>

using namespace std;

class Airplane {
private:
  struct AirplaneRep {
    unsigned long miles;
    char type;
  };

  union {
    AirplaneRep rep;
    Airplane *next; // embedded pointer
  };

public:
  unsigned long getMiles() { return rep.miles; }

  char getType() { return rep.type; }

  void set(unsigned long m, char t) {
    rep.miles = m;
    rep.type = t;
  }

  static void *operator new(size_t size);
  static void operator delete(void *deadObject, size_t size);

private:
  static const int BLOCK_SIZE;
  static Airplane *headOfFreeList;
};

Airplane *Airplane::headOfFreeList;
const int Airplane::BLOCK_SIZE = 512; // memory chunk size

void *Airplane::operator new(size_t size) {
  if (size != sizeof(Airplane)) // only happens on inheritance
    return ::operator new(size);

  Airplane *p = headOfFreeList;
  // set the free list header, because the current is used
  if (p)
    headOfFreeList = p->next;

  else { // allocate a new chunk of memory
    Airplane *newBlock =
        static_cast<Airplane *>(::operator new(BLOCK_SIZE * sizeof(Airplane)));

    // configure the linked list
    for (int i = 1; i < BLOCK_SIZE - 1; ++i)
      newBlock[i].next = &newBlock[i + 1];

    // set the last linked list element
    newBlock[BLOCK_SIZE - 1].next = 0;

    p = newBlock;
    headOfFreeList = &newBlock[1];
  }

  return p;
}

void Airplane::operator delete(void *deadObject, size_t size) {
  if (deadObject == 0)
    return;

  if (size != sizeof(Airplane)) {
    ::operator delete(deadObject);
    return;
  }

  Airplane *carcass = static_cast<Airplane *>(deadObject);

  carcass->next = headOfFreeList;
  headOfFreeList = carcass;
  // never free. Recycle memory to free list.
}

int main() {
  cout << "sizeof(Airplane) = " << sizeof(Airplane) << endl;

  size_t const N = 100;
  Airplane *p[N];

  for (int i = 0; i < N; ++i)
    p[i] = new Airplane;

  p[1]->set(1000, 'A');
  p[5]->set(2000, 'B');
  p[9]->set(500000, 'C');

  for (int i = 0; i < 10; ++i)
    cout << p[i] << endl;

  for (int i = 0; i < N; ++i)
    delete p[i];
}
```

From the output of code, we find that the distance of addresses between two adjacent objects is exactly the size of object. On the other hand, no extra space is needed for the `next` pointer of linked list. The memory region of `next` pointer is overwritten by the object data when it is constructed.

```shell
$ ./allocator2
sizeof(Airplane) = 16
0xc82280
0xc82290
0xc822a0
0xc822b0
0xc822c0
0xc822d0
0xc822e0
0xc822f0
0xc82300
0xc82310
```

## Static Allocators

