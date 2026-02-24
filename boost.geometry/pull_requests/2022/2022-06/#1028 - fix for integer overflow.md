# #1028 [buffer] fix for integer overflow [Merged]

> Username: barendgehrels  
> Created at: 2022-06-29 12:50:56 UTC  
> Updated at: 2022-07-06 06:24:16 UTC  
> Merged at: 2022-07-06 06:24:12 UTC  
> Closed at: 2022-07-06 06:24:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1028  

This fixes issue #998   
A similar issue was found in two other strategies.  
Unit tests are added (apart from the fix for miter)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-06-29 12:51:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1028#pullrequestreview-1023305531  

📁 include/boost/geometry/strategies/cartesian/buffer_join_round.hpp

```diff
 160 |-         Point bp;
 161 |-         set<0>(bp, get<0>(vertex) + vix * prop);
 162 |-         set<1>(bp, get<1>(vertex) + viy * prop);
```

> Username: barendgehrels  
> Created_at: 2022-06-29 12:51:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1028#discussion_r909591563  

This wasn't used...  
We get this performance increase for free :-)


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-06-29 12:57:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1028#pullrequestreview-1023315037  

LGTM, thanks.

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-29 21:05:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1028#pullrequestreview-1024010933  

📁 include/boost/geometry/strategies/cartesian/buffer_join_miter.hpp

```diff
  93 |-         coordinate_type const dy = get<1>(p) - get<1>(vertex);
  92 |+         promoted_type const dx = get<0>(p) - get<0>(vertex);
  93 |+         promoted_type const dy = get<1>(p) - get<1>(vertex);
```

> Username: awulkiew  
> Created_at: 2022-06-29 21:05:14 UTC  
> Updated_at: 2022-06-29 21:05:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1028#discussion_r910412578  

Even though the probability is a lot smaller this could still cause overflow. It's ok for now but we could think about `determinant()` function which would internally perform integral computation using bigger types, potentially some simplified form of multiprecision.

> Username: awulkiew  
> Created_at: 2022-06-29 21:06:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1028#discussion_r910413995  

Or you could first convert to `promoted_type` and subtract afterwards. With this the precision would be lesser but there wouldn't be an overflow in subtraction.

> Username: awulkiew  
> Created_at: 2022-06-29 21:07:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1028#discussion_r910414546  

Btw, there would be a warning here about implicit converson from `int` to `double`.

> Username: barendgehrels  
> Created_at: 2022-07-06 06:24:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1028#discussion_r914456699  

Thanks for your comments. Yes, there could be an overflow in subtraction if coordinates are huge and both plus and minus. I'll create an issue but will not handle it immediately. We probably have many of these kind of places in the library.


---
