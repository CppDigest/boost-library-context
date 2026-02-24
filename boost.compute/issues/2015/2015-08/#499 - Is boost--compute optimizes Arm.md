# #499 - Is boost::compute optimizes Arm ? [Closed]

> Username: ya1gaurav  
> Created at: 2015-08-20 11:35:32 UTC  
> Updated at: 2015-08-25 03:16:27 UTC  
> Closed at: 2015-08-25 03:16:25 UTC  
> Url: https://github.com/boostorg/compute/issues/499  

ARM has neon optimizations. Does boost::compute provide some way for these optimizations?

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-08-20 11:48:38 UTC  
> Url: https://github.com/boostorg/compute/issues/499#issuecomment-132984453  

If by optimized for ARM you mean its algorithms benefits from fact that CPU and GPU are on the same chip etc., then no. Though, Boost.Compute should run on ARM, both its CPU and GPU.  
  
FindOpenCL.cmake is even extended to work on ARM Mali GPUs https://github.com/boostorg/compute/commit/d5fc3170f8185e8e58b82c4b8229b3bef489048f.

---

## Comment 2

> Username: ya1gaurav  
> Created at: 2015-08-20 12:36:57 UTC  
> Url: https://github.com/boostorg/compute/issues/499#issuecomment-132992970  

ok I got your point. Thanks.  
However, there is one more question, as compute is not part of official boost release. Is this module used in many projects? Is there some list of projects published?

---

## Comment 3

> Username: pavanky  
> Created at: 2015-08-20 12:54:29 UTC  
> Url: https://github.com/boostorg/compute/issues/499#issuecomment-132998538  

We use it in [arrayfire](https://github.com/arrayfire/arrayfire) for a bunch of vector algorithms.

---

## Comment 4

> Username: ya1gaurav  
> Created at: 2015-08-20 16:53:31 UTC  
> Url: https://github.com/boostorg/compute/issues/499#issuecomment-133074260  

Ok thanks, If more projects can post usage. We can publish Boost Compute user projects here. It will help spreading more information.  
By the way thanks for fast response & kudos to Boost Compute.  
Please mark this post as question & close.
