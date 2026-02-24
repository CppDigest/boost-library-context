# #1018 - big_constant.hpp:63:52: error: unused parameter 's' [Closed]

> Username: ckormanyos  
> Created at: 2023-08-22 13:12:32 UTC  
> Updated at: 2023-08-23 04:28:23 UTC  
> Closed at: 2023-08-23 04:28:23 UTC  
> Url: https://github.com/boostorg/math/issues/1018  

This may seem really overly pedantic, but I do, in fact, hit a snag on [this](https://github.com/boostorg/math/blob/dc10fc04b29a356c3cf12c2052c45b0c3b3da5f2/include/boost/math/tools/big_constant.hpp#L63).  
  
The subroutine in question is intended to fail a `static_assert` anyway, so there's not really a point using or not using the parameter `s`.  
  
But believe it or not, I get a LINT-er not liking it.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-08-22 17:37:44 UTC  
> Url: https://github.com/boostorg/math/issues/1018#issuecomment-1688636071  

See PR #1019
