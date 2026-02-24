# #532 - Build failure on Windows [Closed]

> Username: pisto  
> Created at: 2015-11-05 11:19:41 UTC  
> Updated at: 2015-11-11 05:06:07 UTC  
> Closed at: 2015-11-11 05:06:01 UTC  
> Url: https://github.com/boostorg/compute/issues/532  

These lines should have `uint_` instead of `uint`: https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/find_if_with_atomics.hpp#L81-L83

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-11-07 17:51:06 UTC  
> Url: https://github.com/boostorg/compute/issues/532#issuecomment-154729945  

Can be closed - see https://github.com/boostorg/compute/pull/533.
