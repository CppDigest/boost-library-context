# #1254 [util] move bounds to geometry::util [Merged]

> Username: barendgehrels  
> Created at: 2024-03-02 16:34:43 UTC  
> Updated at: 2024-04-01 11:29:08 UTC  
> Merged at: 2024-03-26 17:18:21 UTC  
> Closed at: 2024-03-26 17:18:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1254  

This is a follow up on `numeric_cast` - now for `bounds` - which is different and simpler because it was already a structure.  
It also adds unit tests for it.  
So `bounds` for Boost.Rational was stil necessary.  
  
It also adds `CMakeLists.txt` for the folders which tests which were affected

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-02 16:36:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1254#pullrequestreview-1912689688  

📁 include/boost/geometry/util/numeric_cast.hpp

```diff
  39 |-         return boost::rational_cast<Target>(source);
  40 |-     }
  41 |- };
```

> Username: barendgehrels  
> Created_at: 2024-03-02 16:36:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1254#discussion_r1510002249  

This was placed here in last commit, but it is more consistent with other approach if it is all in `rational.hpp` (it was there before as well)


---

## Comment 2

> Username: awulkiew  
> Created_at: 2024-03-03 11:53:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1254#issuecomment-1975134762  

Thanks, I think `util` is a better place for this. We could also consider `math`.  
  
With that said. What do you think about removing it after rewriting or deprecating algorithms using `bounds`? It's because the library supports any coordinate type even big numerical types with dynamic size that have no bounds, e.g. Boost.Mutiprecision. AFAIR in the past we made a decision to not use `assign_inverse` in the library. This utliity was left for backward compatibility and if it was used I'd consider this as a bug. What do you think?  
  
Btw, C++14 has this: https://en.cppreference.com/w/cpp/language/attributes/deprecated

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-03-13 17:32:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1254#issuecomment-1995106751  

> Thanks, I think `util` is a better place for this. We could also consider `math`.  
>   
> With that said. What do you think about removing it after rewriting or deprecating algorithms using `bounds`? It's because the library supports any coordinate type even big numerical types with dynamic size that have no bounds, e.g. Boost.Mutiprecision. AFAIR in the past we made a decision to not use `assign_inverse` in the library. This utliity was left for backward compatibility and if it was used I'd consider this as a bug. What do you think?  
>   
> Btw, C++14 has this: https://en.cppreference.com/w/cpp/language/attributes/deprecated  
  
Didn't answer this yet. But yes, it makes sense. We use it in some places, but not often. And yes, assign_inverse we can deprecate, I forgot it, but I guess it was mainly for spherical/geographic

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2024-03-22 17:53:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1254#issuecomment-2015622727  

There was one comment but I don't think it will change the PR.  
Can it be approved? @awulkiew @vissarion

---

## Comment 5

> Username: awulkiew  
> Created_at: 2024-03-23 20:00:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1254#issuecomment-2016589086  

lgtm thanks.

---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2024-03-26 09:27:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1254#pullrequestreview-1959799840  

Looks good to me, thanks!

---
