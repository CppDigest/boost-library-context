# #1373 Replace boost type_traits with stl type_traits. [Open]

> Username: gogagum  
> Created at: 2025-02-07 21:49:46 UTC  
> Updated at: 2025-05-09 19:25:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1373  

With C++14, is possible to replace **Boost.TypeTraits** library usages with standard `type_traits`. This PR removes direct **Boost.TypeTraits** dependency in **Geometry** module.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-02-07 22:22:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1373#pullrequestreview-2602972566  

📁 include/boost/geometry/index/detail/serialization.hpp

```diff
  66 |     {
  68 |-         return static_cast<T*>(m_storage.address());
  67 |+         return static_cast<void*>(&m_storage);
```

> Username: barendgehrels  
> Created_at: 2025-02-07 22:22:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1373#discussion_r1947269622  

@awulkiew is this fine?

> Username: awulkiew  
> Created_at: 2025-03-05 23:28:30 UTC  
> Updated_at: 2025-03-05 23:29:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1373#discussion_r1982329161  

`std::aligned_storage` is deprecated in C++23. AFAIR boost version is superior to the std one but I can't remember why besides the fact that it doesn't force us to cast address of the storage. A proposed replacement is something like this:  
```  
alignas(T) std::byte storage[sizeof(T)];  
```  
but it requires C++17 and would require careful testing. So I'd prefer to keep `boost::aligned_storage` for now.

> Username: awulkiew  
> Created_at: 2025-03-05 23:37:21 UTC  
> Updated_at: 2025-03-05 23:37:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1373#discussion_r1982335528  

Btw, here https://en.cppreference.com/w/cpp/types/aligned_storage the required use of `std::launder` is mentioned. This change can introduce some unwanted effects. I guess I strongly suggest to keep the old version. :)


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2025-02-07 22:22:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1373#pullrequestreview-2602973665  

Thanks! Looks good to me, one question to Adam

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2025-02-26 18:39:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1373#pullrequestreview-2645494515  

Thanks, this look OK to me.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-04-21 08:48:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1373#issuecomment-2817962555  

@gogagum , will you address the one review action item indicated by @awulkiew ?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2025-05-09 19:21:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1373#issuecomment-2867675039  

@gogagum will you update it or can we close it? (we still can remove the dependency later)

---

## Comment 6

> Username: gogagum  
> Created_at: 2025-05-09 19:25:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1373#issuecomment-2867681048  

@barendgehrels I am going to finish this work.

---
