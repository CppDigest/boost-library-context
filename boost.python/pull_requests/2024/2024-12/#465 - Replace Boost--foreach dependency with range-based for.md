# #465 Replace Boost::foreach dependency with range-based for [Open]

> Username: georgthegreat  
> Created at: 2024-12-21 11:20:45 UTC  
> Updated at: 2025-01-22 10:24:08 UTC  
> Url: https://github.com/boostorg/python/pull/465  

While the library is still marked as c++03 compatible I wonder if the time has come to follow the rest of the boost libraries and raise it up to c++11.

---

## Comment 1

> Username: Osyotr  
> Created_at: 2025-01-05 22:20:25 UTC  
> Url: https://github.com/boostorg/python/pull/465#issuecomment-2571769440  

These public dependencies require C++11:  
- Boost::align  
- Boost::bind  
- Boost::conversion  
- Boost::function  
- Boost::iterator  
- Boost::lexical_cast  
- Boost::smart_ptr  
  
It is perfectly reasonable to drop C++03 support in Boost::python, too.

---

## Review 2 [Commented]

> Username: e-kwsm  
> Created_at: 2025-01-21 19:32:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/465#pullrequestreview-2565527903  

https://github.com/boostorg/python/blob/4fc3afa3ac1a1edb61a92fccd31d305ba38213f8/.github/workflows/test-ubuntu.yml#L14  
  
drop c++98?

---
