# #398 Fix local_buffer test when checking local memory size [Merged]

> Username: kylelutz  
> Created at: 2014-12-30 22:44:11 UTC  
> Updated at: 2014-12-30 23:25:50 UTC  
> Merged at: 2014-12-30 23:25:49 UTC  
> Closed at: 2014-12-30 23:25:49 UTC  
> Url: https://github.com/boostorg/compute/pull/398  

This fixes a test failure for implementations which don't  
properly report dynamically set local memory buffer sizes.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-30 23:01:31 UTC  
> Url: https://github.com/boostorg/compute/pull/398#issuecomment-68408401  

[![Coverage Status](https://coveralls.io/builds/1675596/badge)](https://coveralls.io/builds/1675596)  
  
Coverage decreased (-0.03%) when pulling **c48c4dd0860533806f5fd2deca17005beac39614 on fix-local-buffer-test** into **9f9e6f454301f882e4e89bb0aefcceff10e05e55 on develop**.

---
