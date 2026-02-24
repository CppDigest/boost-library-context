# #4 Fix const_element_containers test failure. [Closed]

> Username: Flast  
> Created at: 2014-08-20 16:54:03 UTC  
> Updated at: 2017-11-06 00:36:14 UTC  
> Closed at: 2017-11-05 16:13:50 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/4  

The test doesn't compile because explicit-instantiation isn't  
  appeared in function-body.  
  
Signed-off-by: Kohei Takahashi flast@flast.jp

---

## Comment 1

> Username: Flast  
> Created_at: 2015-10-08 03:05:54 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/4#issuecomment-146404569  

ping?

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-11-05 16:13:50 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/4#issuecomment-341984623  

Fixed in https://github.com/boostorg/ptr_container/commit/2853f4fb0bffa9065d2525ec40f241ce7c4d4c88.

---
