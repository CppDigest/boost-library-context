# #724 [intersection] refactor segment-intersection class [Merged]

> Username: barendgehrels  
> Created at: 2020-06-10 12:31:39 UTC  
> Updated at: 2020-06-19 21:08:14 UTC  
> Merged at: 2020-06-19 20:05:26 UTC  
> Closed at: 2020-06-19 20:05:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/724  

This is to avoid using both non-rescaled and rescaled coordinates in one method.  
  
It is completely independent of the other PR created today, and should result in no failures

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-06-10 12:32:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/724#pullrequestreview-428003788  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 297 |-         d = geometry::detail::determinant<ResultType>(dx_a, dy_a, dx_b, dy_b);
 298 |-         da = geometry::detail::determinant<ResultType>(dx_b, dy_b, wx, wy);
 299 |-         // Ratio is da/d , collinear if d == 0, intersecting if 0 <= r <= 1
```

> Username: barendgehrels  
> Created_at: 2020-06-10 12:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/724#discussion_r438084726  

this comment was wrong earlier


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2020-06-15 13:22:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/724#pullrequestreview-430637612  

Thanks!  If I understand correctly you avoid creating some robust points in intersection but there are still work todo there. I am ok with merging.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2020-06-17 08:10:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/724#issuecomment-645224384  

>   
> Thanks! If I understand correctly you avoid creating some robust points in intersection but there < are still work todo there. I am ok with merging.  
  
Thanks for your review. Yes, there is still work to do. This PR on itself does not change anything in creating robust points (or avoiding that), the reason is that I want to change things later, and the two coordinate systems in one method (as it was) was a bit confusing and messy. So that is enhanced here.

---

## Review 4 [Approved]

> Username: awulkiew  
> Created_at: 2020-06-19 16:18:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/724#pullrequestreview-434199463  

Thanks!

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-06-19 16:20:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/724#issuecomment-646727438  

Should I mark these changes related to rescaling as enhancements or bugfixes?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2020-06-19 20:05:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/724#issuecomment-646841570  

@awulkiew it's not a bugfix, and indeed it's related to rescaling. So yes, in detail it's an enhancement

---
