# #190 Parallel build on Travis-CI [Merged]

> Username: ddemidov  
> Created at: 2014-07-12 19:25:54 UTC  
> Updated at: 2014-07-12 19:41:20 UTC  
> Merged at: 2014-07-12 19:39:37 UTC  
> Closed at: 2014-07-12 19:39:37 UTC  
> Url: https://github.com/boostorg/compute/pull/190  

This speeds up Travis-CI builds with help of parallel make.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-07-12 19:27:28 UTC  
> Url: https://github.com/boostorg/compute/pull/190#issuecomment-48821597  

Hmm, I thought the travis virtual machines only had a single CPU?

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-07-12 19:29:08 UTC  
> Url: https://github.com/boostorg/compute/pull/190#issuecomment-48821633  

Its a chunky one with 32 cores: https://travis-ci.org/ddemidov/vexcl/jobs/29027698#L346

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-07-12 19:39:39 UTC  
> Url: https://github.com/boostorg/compute/pull/190#issuecomment-48821895  

Awesome! Merged!

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-07-12 19:41:20 UTC  
> Url: https://github.com/boostorg/compute/pull/190#issuecomment-48821929  

[![Coverage Status](https://coveralls.io/builds/960603/badge)](https://coveralls.io/builds/960603)  
  
Changes Unknown when pulling **57a2ee0e39b4d0647ce2af782ecd457249ced148 on ddemidov:parallel-travis-build** into *\* on kylelutz:develop**.

---
