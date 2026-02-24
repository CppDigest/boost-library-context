# #150 - Objects in shared memory require more memory then they need [Closed]

> Username: WDMdanila  
> Created at: 2021-08-31 06:34:14 UTC  
> Updated at: 2021-08-31 11:34:45 UTC  
> Closed at: 2021-08-31 11:34:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/150  

**Boost version**: 1.76.0  
**Operating System**: Ubuntu / Debian  
**Language**: C++20, C++17  
  
## Expected behaviour  
Object `T` created in `boost::interprocess::managed_shared_memory` requires that `managed_shared_memory` should be exactly `sizeof(T)`  
  
## Actual behaviour  
When creating `boost::interprocess::managed_shared_memory` for storing shared objects with:  
  
```cpp  
auto SEGMENT_SIZE = sizeof(T);  
managed_shared_memory segment(boost::interprocess::create_only, "TestSharedMemory", SEGMENT_SIZE);  
auto *object = segment.construct<T>("ShBuffObj")(&segment);  
```  
we get this error: `boost::interprocess::bad_alloc`  
  
After some testing and examination we found out that extra space required was `492` bytes and that it would change in increments of `100` and exact size depends on the name of object created.  
  
## Question itself  
Could someone explain what is going on and how could we calculate exactly how much space `boost::interprocess::managed_shared_memory` should have in advance?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-31 10:20:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/150#issuecomment-909102561  

It isn't expected an object can use sizeof(T) because of several reasons, just like `T *p = new T` doesn't use sizeof(T) bytes from the heap:  
  
- Dynamic memory needs bookkeeping data structures to be able to find free memory and deallocate memory.  
- You need to store the number of objects you are allocating (in case it's an array, you need to store the size and the number of destructores to call when destroying the named object)  
- You need to store the name of the object ("ShBuffObj") somewhere.  
- You need to implement a name->object map data structure to be able to find the object by name (think of it as an additional std::map in shared memory that stores const char *name / void * object address).  
- Alignment restrictions (in 64 bit systems every allocation is round up to 8 bytes).  
- Available size depends on how previous allocations have fragmented the memory (you can have spots of previously deallocated objects that can't be reused by new objects because old objects were smaller).  
  
In general, there is no formula to precisely know how much memory you will end up using, just like you don't exactly know how much heap you will consume with dynamic allocation using new/delete/malloc/free.

---

## Comment 2

> Username: WDMdanila  
> Created at: 2021-08-31 11:34:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/150#issuecomment-909153421  

Thank you very much for quite in-depth explanation and such a quick response!
