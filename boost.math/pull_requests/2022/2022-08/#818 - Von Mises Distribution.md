# #818 Von Mises Distribution [Open]

> Username: mborland  
> Created at: 2022-08-15 03:41:33 UTC  
> Updated at: 2022-10-29 17:13:14 UTC  
> Url: https://github.com/boostorg/math/pull/818  

#330 looked very close to completion so this is my attempt to foster it. Also see https://github.com/boostorg/math/issues/296

---

## Comment 1

> Username: mborland  
> Created_at: 2022-08-26 22:52:36 UTC  
> Url: https://github.com/boostorg/math/pull/818#issuecomment-1229024176  

@NAThompson This should be good for review. The only major outstanding change from the last cut at this was removing all the double precision magic numbers which I have. I would have just made the edits on the linked PR, but since the guy deleted his account I can't access the branch.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2022-08-27 01:02:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/818#pullrequestreview-1087616756  

📁 doc/distributions/von_mises.qbk

```diff
  41 |+ [h4 Member Functions]
  42 |+ 
  43 |+    von_mises_distribution(RealType mean = 0, RealType concentration = 1); ;
```

> Username: NAThompson  
> Created_at: 2022-08-27 01:02:15 UTC  
> Url: https://github.com/boostorg/math/pull/818#discussion_r956515468  

@jzmaddock : IIRC we now use `Real` instead of `RealType`? Obviously a nitpick.


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2022-08-27 01:04:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/818#pullrequestreview-1087616977  

📁 doc/distributions/von_mises.qbk

```diff
  70 |+ that are generic to all distributions are supported: __usual_accessors.
  71 |+ 
  72 |+ The domain of the random variable is O <= x <= 2[pi].
```

> Username: NAThompson  
> Created_at: 2022-08-27 01:04:09 UTC  
> Url: https://github.com/boostorg/math/pull/818#discussion_r956515713  

Maybe use “≤” (U+2264) which I think is \u2264 in .qbk?


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2022-08-27 01:06:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/818#pullrequestreview-1087617227  

📁 include/boost/math/distributions/detail/common_error_handling.hpp

```diff
 113 |+       *result = policies::raise_domain_error<RealType>(
 114 |+          function,
 115 |+          "Angle parameter is %1%, but must be between -pi and +pi!", angle, pol);
```

> Username: NAThompson  
> Created_at: 2022-08-27 01:06:01 UTC  
> Url: https://github.com/boostorg/math/pull/818#discussion_r956515891  

If the CDF is periodic, is it sensible to restrict the angle like this?


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2022-08-27 01:07:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/818#pullrequestreview-1087617407  

📁 doc/distributions/von_mises.qbk

```diff
  74 |+ [h4 Accuracy]
  75 |+ 
  76 |+ The CDF of this distribution is not analytic so an approximation is made.
```

> Username: NAThompson  
> Created_at: 2022-08-27 01:07:18 UTC  
> Url: https://github.com/boostorg/math/pull/818#discussion_r956516009  

Does the approximation mean that multiprecision types should be rejected?


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2022-08-27 01:15:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/818#pullrequestreview-1087619606  

📁 test/test_von_mises.cpp

```diff
 381 |+   test_symmetry(0.0F);
 382 |+   test_symmetry(0.0);
 383 |+   test_symmetry(0.0L);
```

> Username: NAThompson  
> Created_at: 2022-08-27 01:15:55 UTC  
> Url: https://github.com/boostorg/math/pull/818#discussion_r956517245  

Might we test the quadrature of the pdf = 1?


---

## Comment 7

> Username: NAThompson  
> Created_at: 2022-08-27 01:16:45 UTC  
> Url: https://github.com/boostorg/math/pull/818#issuecomment-1229087055  

@mborland : I've approved, but with the caveat that I don't know much about this. We'll probably want a second review from @jzmaddock .

---

## Comment 8

> Username: SiliconA-Z  
> Created_at: 2022-10-25 02:37:48 UTC  
> Url: https://github.com/boostorg/math/pull/818#issuecomment-1289898097  

Any update on this?

---
