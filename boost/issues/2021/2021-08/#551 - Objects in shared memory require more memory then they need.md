# #551 - Objects in shared memory require more memory then they need [Closed]

> Username: WDMdanila  
> Created at: 2021-08-31 06:30:15 UTC  
> Updated at: 2021-08-31 11:35:13 UTC  
> Closed at: 2021-08-31 11:35:13 UTC  
> Url: https://github.com/boostorg/boost/issues/551  

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

> Username: WDMdanila  
> Created at: 2021-08-31 11:35:13 UTC  
> Url: https://github.com/boostorg/boost/issues/551#issuecomment-909153690  

Wrong project, sorry
