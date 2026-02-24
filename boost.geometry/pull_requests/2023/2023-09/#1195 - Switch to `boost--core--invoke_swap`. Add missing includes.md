# #1195 Switch to `boost::core::invoke_swap`. Add missing includes. [Merged]

> Username: Lastique  
> Created at: 2023-09-03 00:26:59 UTC  
> Updated at: 2023-12-04 10:44:44 UTC  
> Merged at: 2023-09-08 10:17:22 UTC  
> Closed at: 2023-09-08 10:17:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1195  

`boost::swap` is deprecated and will be removed. Use `boost::core::invoke_swap` as a replacement.  
  
Also add missing includes.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-09-05 18:22:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1195#pullrequestreview-1611682836  

📁 include/boost/geometry/formulas/karney_inverse.hpp

```diff
 452 |+             boost::core::invoke_swap(sin_alpha1, sin_alpha2);
 453 |+             boost::core::invoke_swap(cos_alpha1, cos_alpha2);
 454 |+             boost::core::invoke_swap(result.geodesic_scale, M21);
```

> Username: barendgehrels  
> Created_at: 2023-09-05 18:22:24 UTC  
> Updated_at: 2023-09-05 18:22:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1195#discussion_r1316246225  

We can, if I see it correctly, replace these calls (here) with just `std::swap`  
And maybe on more places.  
But for now this PR looks okay to me.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2023-09-08 10:17:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1195#pullrequestreview-1617150205  

Thanks, I am OK with merging.   
  
ps. there are some CI tests failing but probably not related to this PR

---

## Comment 3

> Username: Lastique  
> Created_at: 2023-09-08 10:35:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1195#issuecomment-1711452393  

The test failures show that some Boost.Geometry headers are incomplete and probably broken. I didn't know how to fix it, and it wasn't the point of this PR anyway.  
  
For example, [this](https://github.com/boostorg/geometry/actions/runs/6061347079/job/16446454333#step:7:281) seems to rely on `get` being found by ADL, but ADL is not performed when template parameters are explicitly specified. At least, not in C++17 that is used by default in gcc 11. Or, if that `get` should be available without ADL, it is not available, so an include must be missing (I don't know which).

---
