# #1209 Use fp_equals comparison in side_strategy for convex_hull. [Merged]

> Username: tinko92  
> Created at: 2023-10-16 17:52:00 UTC  
> Updated at: 2024-02-13 09:11:19 UTC  
> Merged at: 2024-02-13 09:11:10 UTC  
> Closed at: 2024-02-13 09:11:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1209  

This changes the default side strategy for convex_hull for cartesian coordinates so that it uses an exact comparison to zero for the side value. This is IMHO the only way to use that strategy to guarantee exact (and thereby consistent) results, which was the intention behind the strategy. The motivation is issue #1208 , which is fixed by this change. I see no testcase breaking.  
  
From git blame, I don't see the motivation for the current non-exact comparison. If it was to be more lenient in is_convex, which uses the same umbrella strategy, then maybe those should be separate umbrella strategies. I think in the construction of the hull this non-robustness is more problematic. If it was done like this to avoid near-collinear points on edges of the output, maybe there should be a separate iteration over the hull after construction with another side-strategy.

---

## Comment 1

> Username: atalon-lip6  
> Created_at: 2023-10-18 11:35:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1209#issuecomment-1768261934  

I can confirm it solves a case where Boost was not including one point with maximum x-coordinates in the convex hull.

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2023-12-01 14:01:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1209#pullrequestreview-1759868761  

Thanks, I agree with this change. I also verified that all test are passing.

---

## Comment 3

> Username: vissarion  
> Created_at: 2023-12-01 14:02:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1209#issuecomment-1836169064  

Let's wait for @barendgehrels to approve, who has been involved in those changes of robust and non robust strategies.

---

## Review 4 [Approved]

> Username: barendgehrels  
> Created_at: 2024-02-10 17:49:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1209#pullrequestreview-1873842931  

Sorry for the delay. I'm OK

---
