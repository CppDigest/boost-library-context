# #1365 [formulas] Fix warning (variable hiding member) in sjoberg_intersection [Merged]

> Username: awulkiew  
> Created at: 2025-01-11 22:37:20 UTC  
> Updated at: 2025-03-18 13:20:25 UTC  
> Merged at: 2025-01-13 13:37:49 UTC  
> Closed at: 2025-01-13 13:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1365  

This variable is already calculated in the constructor

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-12 07:29:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1365#pullrequestreview-2545365586  

📁 include/boost/geometry/formulas/sjoberg_intersection.hpp

```diff
 567 |         CT const c0 = 0;
 568 |         CT const dLj = d_lambda(c0);
 569 |-         CT const asin_tj_t0j = asin(Cj * tan_betaj / sqrt_1_Cj_sqr);
```

> Username: barendgehrels  
> Created_at: 2025-01-12 07:29:37 UTC  
> Updated_at: 2025-01-12 07:29:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1365#discussion_r1912384995  

Thanks. So it's a member var and indeed calculated in the constructor.  
👍

> Username: tinko92  
> Created_at: 2025-01-12 10:23:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1365#discussion_r1912416716  

This looks correct assuming, Cj, tan_betaj and sqrt_1_Cj_sqr are all constant between construction and every call of this function (they are public, mutable members in a non-detail-namespaced class but since the class is undocumented, I assume it is not meant to be a public interface).

> Username: awulkiew  
> Created_at: 2025-01-12 13:58:19 UTC  
> Updated_at: 2025-01-12 14:01:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1365#discussion_r1912458068  

Indeed they are public and shouldn't be. And there is a lot of them so it'd be easy to make a mistake. For now I made geodesics const in the code using them.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2025-01-13 10:55:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1365#pullrequestreview-2546297145  

Thanks for this fix Adam. I agree with merging.

---
