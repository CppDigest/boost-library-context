# #339 Allow variant deserialization to make types with private constructors [Open]

> Username: KJTsanaktsidis  
> Created at: 2025-12-27 00:24:35 UTC  
> Updated at: 2026-02-06 08:56:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/339  

If a type has a private no-args constructor, but has friended boost::serialization::access, it can usually be deserialised as normal. However, it _can't_ be deserialized out of a variant, because the variant tries to invoke the default constructor.  
  
Fix this by constructing it through boost::serialization::access, which is designed to model "classes that can't be created with no arguments except through deserialization"

---

## Review 1 [Commented]

> Username: KJTsanaktsidis  
> Created_at: 2026-02-06 08:56:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/339#pullrequestreview-3761639970  

📁 include/boost/serialization/access.hpp

```diff
 131 |     }
 132 |+     template<class T>
 133 |+     static T *construct_r(void * t) {
```

> Username: KJTsanaktsidis  
> Created_at: 2026-02-06 08:56:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/339#discussion_r2773010531  

Hm, had to add a new overload of `construct` that actually returned the placement-new result, because of pointer provenance issues.  
  
```  
alignas(T) unsigned char mem[sizeof(T)];  
T *ptr = reinterpret_cast<T*>(mem);  
::new(ptr)T;  
  
ptr->stuff();  
```  
  
Apparently is no bueno because the compiler can assume that ptr is NOT the same thing as the in-place-constructed T. Need to instead do something like  
  
```  
alignas(T) unsigned char mem[sizeof(T)];  
T *ptr = ::new(mem)T;  
  
ptr->stuff();  
```


---
