# #48 Use SHA1 for online cache keys [Closed]

> Username: ddemidov  
> Created at: 2014-01-07 18:40:35 UTC  
> Updated at: 2014-01-07 18:56:45 UTC  
> Closed at: 2014-01-07 18:56:45 UTC  
> Url: https://github.com/boostorg/compute/pull/48  

This makes online cache use SHA1 of the program source as key. Introduces `compute::detail::sha1()` function, which is moved from `compute::program` into its own header file.  
  
This does not change the total test time on my Intel CPU. What really should be tested though is a sequence of kernels launched in a loop.

---

## Comment 1

> Username: ddemidov  
> Created_at: 2014-01-07 18:42:06 UTC  
> Url: https://github.com/boostorg/compute/pull/48#issuecomment-31765713  

I've picked the wrong branch, sorry. I can reopen if necessary.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-01-07 18:54:58 UTC  
> Url: https://github.com/boostorg/compute/pull/48#issuecomment-31766971  

Looks good! I was planning on moving `sha1()` to its own header anyway. Should be a useful utility function.  
  
Also you should be able to remove the `<boost/functional/hash.hpp>` include from `meta_kernel.hpp`.  
  
And yeah, can you target the pull request at the `develop` branch. I'm trying to get into the habit of pushing and testing code on `develop` before moving into the (stable) `master` :-).  
  
Thanks!

---
