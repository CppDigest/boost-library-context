# #51 Fix integer rollover that triggers clang ubsan when U is unsigned [Closed]

> Username: garyfurnish  
> Created at: 2017-06-06 00:21:52 UTC  
> Updated at: 2017-06-06 12:47:23 UTC  
> Closed at: 2017-06-06 12:47:23 UTC  
> Url: https://github.com/boostorg/container/pull/51  

If U is unsigned in destroy_alloc_n, n-- can lead to unsigned integer rollover when n is 0.  
This behavior, while trivial and defined, is sufficient to trigger clang's ubsan detector and create excess noise.  
  
> /mnt/fscratch0/pub/dependencies/boost/boost/container/detail/copy_move_algo.hpp:966:11: runtime error: unsigned integer overflow: 0 - 1 cannot be represented in type 'unsigned long'

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-06-06 12:47:23 UTC  
> Url: https://github.com/boostorg/container/pull/51#issuecomment-306475367  

Thanks for the patch. Added to develop branch, will merge in time for Boost 1.65

---
