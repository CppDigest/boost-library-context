# #79 Performance improvement: [Merged]

> Username: BrianWeed  
> Created at: 2023-06-22 15:50:15 UTC  
> Updated at: 2023-06-23 12:33:25 UTC  
> Merged at: 2023-06-22 18:06:19 UTC  
> Closed at: 2023-06-22 18:06:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/79  

Add move semantics to by-value parameters (iterator and predicate), to eliminate the cost of copy-construction. Both the Predicate (Normally a std::function, or lambda), and custom iterators can have state, which can be expensive to copy. Profiler identified this as a bottleneck while using boost::adaptors::filtered.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-06-22 17:09:34 UTC  
> Url: https://github.com/boostorg/iterator/pull/79#issuecomment-1603027547  

We don't require C++11 in Boost.Iterator. Yet, anyway.

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2023-06-22 17:32:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/79#pullrequestreview-1493701926  

📁 include/boost/iterator/filter_iterator.hpp

```diff
  16 |+ #if __cplusplus >= 201103L
  17 |+ #include <utility>
  18 |+ #define STD_MOVE(x) std::move(x)
```

> Username: Lastique  
> Created_at: 2023-06-22 17:32:37 UTC  
> Updated_at: 2023-06-22 17:32:38 UTC  
> Url: https://github.com/boostorg/iterator/pull/79#discussion_r1238837322  

No, this won't do. We don't define macros not qualified with a library prefix and since this is an internal macro, it should include `DETAIL` (e.g. `BOOST_ITERATOR_DETAIL_MOVE` or something) and preferably undefined at the end of the file. And the check for `__cplusplus >= 201103L` is also not correct because not all compilers set `__cplusplus` accordingly. You should use `BOOST_NO_CXX11_RVALUE_REFERENCES` defined by Boost.Config, and preferably avoid using `std::move` and including `<utility>`.


---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2023-06-22 17:57:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/79#pullrequestreview-1493739425  

📁 include/boost/iterator/filter_iterator.hpp

```diff
  15 | 
  16 |- #if __cplusplus >= 201103L
  16 |+ #ifndef BOOST_NO_CXX11_RVALUE_REFERENCES
```

> Username: Lastique  
> Created_at: 2023-06-22 17:57:15 UTC  
> Url: https://github.com/boostorg/iterator/pull/79#discussion_r1238862080  

Missing `#include <boost/config.hpp>`.


---

## Comment 4

> Username: Lastique  
> Created_at: 2023-06-22 18:06:28 UTC  
> Url: https://github.com/boostorg/iterator/pull/79#issuecomment-1603102565  

Thanks.

---
