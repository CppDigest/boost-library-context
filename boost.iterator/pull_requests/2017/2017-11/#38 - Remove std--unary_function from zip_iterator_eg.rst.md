# #38 Remove std::unary_function from zip_iterator_eg.rst [Merged]

> Username: pavelkryukov  
> Created at: 2017-11-25 09:16:12 UTC  
> Updated at: 2017-11-25 19:38:47 UTC  
> Merged at: 2017-11-25 18:58:42 UTC  
> Closed at: 2017-11-25 18:58:42 UTC  
> Url: https://github.com/boostorg/iterator/pull/38  



---

## Comment 1

> Username: eldiener  
> Created_at: 2017-11-25 15:23:38 UTC  
> Url: https://github.com/boostorg/iterator/pull/38#issuecomment-346946956  

This change should only occur when the Boost Config macro BOOST_NO_CXX98_FUNCTION_BASE is defined.

---

## Comment 2

> Username: pavelkryukov  
> Created_at: 2017-11-25 15:54:04 UTC  
> Url: https://github.com/boostorg/iterator/pull/38#issuecomment-346948805  

Why? In this example `zip_func()` is passed only to `std::for_each` , it did not demand unary functions to be inherited from `std::unary_function` AFAIK.

---
