# #249 Better perf fail warning [Merged]

> Username: edbaunton  
> Created at: 2014-08-25 18:16:23 UTC  
> Updated at: 2014-08-26 01:39:23 UTC  
> Merged at: 2014-08-26 01:38:48 UTC  
> Closed at: 2014-08-26 01:38:48 UTC  
> Url: https://github.com/boostorg/compute/pull/249  

I blindly ran `perf.py` and it then silently fails and gives you outputs of zero for all tests.  
  
This will at least print a warning if the executable has not been compiled for running the test.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-08-25 18:47:57 UTC  
> Url: https://github.com/boostorg/compute/pull/249#issuecomment-53309450  

[![Coverage Status](https://coveralls.io/builds/1127174/badge)](https://coveralls.io/builds/1127174)  
  
Coverage remained the same when pulling **0be4019ef5ff34888fd303ec0d387ee2930bd263 on edbaunton:better-perf-fail-warning** into **a2db6fe69999e147060b22c72e4584d6db103865 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-08-26 01:39:23 UTC  
> Url: https://github.com/boostorg/compute/pull/249#issuecomment-53363298  

Awesome! The `perf.py` script could definitely use some work, thanks for fixing it!

---
