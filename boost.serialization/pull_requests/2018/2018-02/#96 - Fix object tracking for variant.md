# #96 Fix object tracking for variant [Closed]

> Username: ricardocosme  
> Created at: 2018-02-24 13:57:34 UTC  
> Updated at: 2018-02-24 17:45:07 UTC  
> Closed at: 2018-02-24 14:56:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/96  

Fix object tracking for a pointer to an object contained into a variant that is an element of a container which loads a `value_type` into a local object. For example a `std::map` with a `boost::variant` as a `mapped_type`.  
  
This commit supports:  
- map  
- multimap  
- unordered_map  
- unordered_multimap  
- set  
- multiset  
- unordered_set  
- unordered_multiset

---

## Comment 1

> Username: ricardocosme  
> Created_at: 2018-02-24 14:56:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/96#issuecomment-368233804  

Build failed for some platforms. I will fix this.

---
