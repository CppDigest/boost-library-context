# #883 Fix various warnings, e.g. remove unused variables, etc. [Merged]

> Username: awulkiew  
> Created at: 2021-07-05 12:17:33 UTC  
> Updated at: 2021-07-06 14:11:58 UTC  
> Merged at: 2021-07-06 14:11:58 UTC  
> Closed at: 2021-07-06 14:11:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/883  



---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-07-05 13:15:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/883#pullrequestreview-699168435  

📁 test/geometries/initialization.cpp

```diff
 239 |         //v = boost::assign::list_of(P(1, 1))(P(2, 2))(P(3, 3));
 240 |-         v.empty();
 240 |+         //v.empty();
```

> Username: vissarion  
> Created_at: 2021-07-05 13:15:09 UTC  
> Updated_at: 2021-07-05 13:15:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/883#discussion_r663923307  

what was the problem with `empty` here?

> Username: awulkiew  
> Created_at: 2021-07-05 14:10:32 UTC  
> Updated_at: 2021-07-05 14:14:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/883#discussion_r663961131  

Ignoring the return value of a function declared with `[[nodiscard]]`.  
  
```  
warning: ignoring return value of 'bool std::vector<...>::empty() const ...', declared with attribute 'nodiscard' [-Wunused-result]  
```  
  
https://en.cppreference.com/w/cpp/language/attributes/nodiscard  
  
e.g. reported here: https://www.boost.org/development/tests/develop/output/teeks99-dkr-dg11-opt-geometry-gcc-11~c++20~O2-warnings.html#algorithms_area_geo  
  
This could also be fixed by storing the return value in variable and checking it or passing to `ignore_unused()` but I don't think that this call is needed at all. But since I'm not sure about it, in case it was needed I commented it out for now instead of removing it.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2021-07-06 14:11:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/883#issuecomment-874796049  

This fix is simple so I'll merge it now.

---
