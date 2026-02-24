# #38 - Use `while_` instead of recursion when possible [Closed]

> Username: ldionne  
> Created at: 2015-04-09 05:28:11 UTC  
> Updated at: 2015-08-18 19:51:00 UTC  
> Closed at: 2015-08-18 19:51:00 UTC  
> Url: https://github.com/boostorg/hana/issues/38  



---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-18 19:51:00 UTC  
> Url: https://github.com/boostorg/hana/issues/38#issuecomment-132331261  

This is not relevant anymore, since we now compute indices with `constexpr` and then unpack them in `O(1)` runtime complexity, which is superior to any `while_`-based implementation.
