# #870 remove the redundant copy when move item to the front of the lru list in lru_cache.hpp [Open]

> Username: faker2048  
> Created at: 2021-10-04 06:23:48 UTC  
> Updated at: 2021-10-04 06:23:48 UTC  
> Url: https://github.com/boostorg/compute/pull/870  

std::list<T,Allocator>::splice is the helper for to move list element in constant complexity.  
  
  
Complexity  
1-2) Constant.  
3) Constant if other refers to the same object as *this, otherwise linear in std::distance(first, last).  
  
Ref: https://en.cppreference.com/w/cpp/container/list/splice

---
