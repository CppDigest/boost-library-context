# #56 close_at_tolerance: changed t >= 0 to t >= FPT(0) [Merged]

> Username: akrzemi1  
> Created at: 2015-05-12 07:24:28 UTC  
> Updated at: 2015-05-12 07:32:32 UTC  
> Merged at: 2015-05-12 07:32:32 UTC  
> Closed at: 2015-05-12 07:32:32 UTC  
> Url: https://github.com/boostorg/test/pull/56  

My floating point type is not implicitly convertible from literal 0. close_at_tolerance should work with it as well.

---
