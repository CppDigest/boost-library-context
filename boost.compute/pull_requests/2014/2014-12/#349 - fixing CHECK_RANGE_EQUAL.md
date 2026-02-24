# #349 fixing CHECK_RANGE_EQUAL [Merged]

> Username: sschaetz  
> Created at: 2014-12-17 20:38:35 UTC  
> Updated at: 2014-12-17 21:58:08 UTC  
> Merged at: 2014-12-17 21:57:55 UTC  
> Closed at: 2014-12-17 21:57:56 UTC  
> Url: https://github.com/boostorg/compute/pull/349  

if the container passed to CHECK_RANGE_EQUAL is larger than the expected range, the copy writes over the boundaries of the created buffer _actual.  
  
This fixes unit tests 27 - algorithm.copy_if and 46 - algorithm.merge on ARM plattform but should be an error on all plattforms.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-17 20:54:54 UTC  
> Url: https://github.com/boostorg/compute/pull/349#issuecomment-67392071  

[![Coverage Status](https://coveralls.io/builds/1626878/badge)](https://coveralls.io/builds/1626878)  
  
Coverage remained the same when pulling **ea7e0a099300a958732f90af24375fede60a1290 on sschaetz:develop** into **d8eae205cee5b38f076926b3966d93d70ffebfb6 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-12-17 21:58:08 UTC  
> Url: https://github.com/boostorg/compute/pull/349#issuecomment-67402278  

Good catch! Thanks for fixing this!

---
