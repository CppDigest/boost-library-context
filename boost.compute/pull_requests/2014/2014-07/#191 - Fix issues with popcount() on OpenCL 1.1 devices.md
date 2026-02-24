# #191 Fix issues with popcount() on OpenCL 1.1 devices [Merged]

> Username: kylelutz  
> Created at: 2014-07-12 19:49:04 UTC  
> Updated at: 2014-07-12 20:06:14 UTC  
> Merged at: 2014-07-12 20:03:09 UTC  
> Closed at: 2014-07-12 20:03:09 UTC  
> Url: https://github.com/boostorg/compute/pull/191  



---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-07-12 19:49:30 UTC  
> Url: https://github.com/boostorg/compute/pull/191#issuecomment-48822146  

@ddemidov this should fix the rest of the issues on the K40c.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-07-12 20:02:30 UTC  
> Url: https://github.com/boostorg/compute/pull/191#issuecomment-48822490  

It does. The tests run cleanly on K40c.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-07-12 20:03:02 UTC  
> Url: https://github.com/boostorg/compute/pull/191#issuecomment-48822508  

Great! Thanks!

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-07-12 20:06:14 UTC  
> Url: https://github.com/boostorg/compute/pull/191#issuecomment-48822589  

[![Coverage Status](https://coveralls.io/builds/960650/badge)](https://coveralls.io/builds/960650)  
  
Coverage increased (+0.0%) when pulling **61645c41c34687f58fbc6acca41e58540a6b3fb3 on fix-popcount** into **4bf8993f1bf3d6c8abf730cace769eb87f08c63c on develop**.

---
