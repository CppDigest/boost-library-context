# #51 Improve stl compat [Merged]

> Username: HDembinski  
> Created at: 2018-07-02 11:11:57 UTC  
> Updated at: 2018-07-12 22:42:32 UTC  
> Merged at: 2018-07-04 23:19:12 UTC  
> Closed at: 2018-07-04 23:19:12 UTC  
> Url: https://github.com/boostorg/histogram/pull/51  

Improve compatibility with STL algorithms.  
  
issue #45: use operator() to fill to use std::for_each for simple cases  
issue #44: removed sum(), one should use std::for_each or std::accumulate instead for simple algorithms like this  
  
added support for stl containers as fill and index arguments with additional TMP

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-07-03 09:54:22 UTC  
> Updated_at: 2018-07-04 23:29:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/51#issuecomment-402085659  

[![Coverage Status](https://coveralls.io/builds/17836813/badge)](https://coveralls.io/builds/17836813)  
  
Coverage decreased (-0.3%) to 98.471% when pulling **6c5b56d1bca8c6b227e82bb3f8d474ff0c2698f9 on improve_stl_compat** into **7ab49671d500b94c15ae9fa648b87a2a655274ee on develop**.

---
