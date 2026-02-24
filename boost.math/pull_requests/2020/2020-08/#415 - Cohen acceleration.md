# #415 Cohen acceleration [Merged]

> Username: NAThompson  
> Created at: 2020-08-07 19:50:42 UTC  
> Updated at: 2020-08-10 14:17:14 UTC  
> Merged at: 2020-08-09 13:55:57 UTC  
> Closed at: 2020-08-09 13:55:57 UTC  
> Url: https://github.com/boostorg/math/pull/415  

Accelerates convergence of an alternating series by a method designed by Cohen, Villegas, and Zagier.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-08-08 01:13:26 UTC  
> Url: https://github.com/boostorg/math/pull/415#issuecomment-670801943  

@jzmaddock : You gotta give this one a try; it's amazing.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-08-08 08:56:17 UTC  
> Url: https://github.com/boostorg/math/pull/415#issuecomment-670847153  

> You gotta give this one a try; it's amazing.  
  
We used this for something - Owen's T I think - I'll look at your code later.  I do remember I contemplated boilerplating this, but had mixed results: it was brilliant when it worked, but in the difficult cases often failed (and it was the slow/non-converging cases I was interested in at the time).  Am I right in thinking this is method 1 from the paper?  I never quite got my head around the other methods :(

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-08-08 12:48:13 UTC  
> Url: https://github.com/boostorg/math/pull/415#issuecomment-670925345  

> Am I right in thinking this is method 1 from the paper? I never quite got my head around the other methods :(  
  
It is method 1. The other methods only are useful when calculation of the terms is extremely expensive. I thought about adding those, but my use case really didn't require that.  
  
BTW, should this go in `series.hpp` or is it fine where it is?

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2020-08-09 09:57:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/415#pullrequestreview-463859851  

📁 doc/internals/cohen_acceleration.qbk

```diff
  73 |+ [heading Performance]
  74 |+ 
  75 |+ To see that Cohen acceleration is in fact faster than naive summation for the same level of relative accuracy, we can run the `reporting/performance/cohen_acceleration_performance.cpp` file:
```

> Username: jzmaddock  
> Created_at: 2020-08-09 09:57:24 UTC  
> Updated_at: 2020-08-09 13:55:25 UTC  
> Url: https://github.com/boostorg/math/pull/415#discussion_r467562713  

Can we mention what the test sum is in the docs here?  Thanks!


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-08-09 09:59:33 UTC  
> Url: https://github.com/boostorg/math/pull/415#issuecomment-671032183  

I think it's fine as it is IMO.  Other than the minor comment on the doc, looks all good to go?

---
