# #49 Use SHA1 for online cache keys [Merged]

> Username: ddemidov  
> Created at: 2014-01-07 18:58:09 UTC  
> Updated at: 2014-01-07 19:52:59 UTC  
> Merged at: 2014-01-07 19:10:10 UTC  
> Closed at: 2014-01-07 19:10:10 UTC  
> Url: https://github.com/boostorg/compute/pull/49  

This makes online cache use SHA1 of the program source as key. Introduces `compute::detail::sha1()` function, which is moved from `compute::program` into its own header file.  
  
This does not change the total test time on my Intel CPU. What really should be tested though is a sequence of kernels launched in a loop.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-01-07 19:05:08 UTC  
> Url: https://github.com/boostorg/compute/pull/49#issuecomment-31767997  

Hmm. Can't merge through GitHub. Can you rebase on current `develop`.  
  
Thanks.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-01-07 19:07:57 UTC  
> Url: https://github.com/boostorg/compute/pull/49#issuecomment-31768299  

Done

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-01-07 19:10:23 UTC  
> Url: https://github.com/boostorg/compute/pull/49#issuecomment-31768508  

Merged!

---
