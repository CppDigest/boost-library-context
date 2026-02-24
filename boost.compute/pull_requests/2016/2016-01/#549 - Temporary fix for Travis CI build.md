# #549 Temporary fix for Travis CI build [Closed]

> Username: jszuppe  
> Created at: 2016-01-08 18:25:14 UTC  
> Updated at: 2016-05-01 11:20:07 UTC  
> Closed at: 2016-01-17 20:03:32 UTC  
> Url: https://github.com/boostorg/compute/pull/549  

See https://github.com/travis-ci/travis-ci/issues/5221.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-01-08 19:06:40 UTC  
> Url: https://github.com/boostorg/compute/pull/549#issuecomment-170094895  

Well, it didn't work.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-01-12 04:13:12 UTC  
> Url: https://github.com/boostorg/compute/pull/549#issuecomment-170781882  

That's unfortunate... I'm not sure what we should do with Travis-CI now that the new container-based infrastructure doesn't support OpenCL. I wonder if we should try with a different OpenCL implementation (maybe Intel or POCL)?

---

## Comment 3

> Username: ddemidov  
> Created_at: 2016-01-12 04:28:35 UTC  
> Url: https://github.com/boostorg/compute/pull/549#issuecomment-170786332  

Did you guys look at https://drone.io/?

---

## Comment 4

> Username: ddemidov  
> Created_at: 2016-01-12 06:34:49 UTC  
> Url: https://github.com/boostorg/compute/pull/549#issuecomment-170813246  

It does work, although the limit for a single job of 15 minutes is a bit tight: https://drone.io/github.com/ddemidov/vexcl/3.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2016-01-17 17:12:50 UTC  
> Url: https://github.com/boostorg/compute/pull/549#issuecomment-172354096  

Sorry for all those commits, but I'm trying to make it work on Trusty. When it works I'll make a proper squash.

---

## Comment 6

> Username: jszuppe  
> Created_at: 2016-01-17 18:13:25 UTC  
> Url: https://github.com/boostorg/compute/pull/549#issuecomment-172359933  

Later this week I'll try using Intel OpenCL driver.

---
