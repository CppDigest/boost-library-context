# #582 Remove boost.predef dependency [Merged]

> Username: mborland  
> Created at: 2021-03-25 16:12:28 UTC  
> Updated at: 2021-03-28 11:20:52 UTC  
> Merged at: 2021-03-28 10:33:40 UTC  
> Closed at: 2021-03-28 10:33:40 UTC  
> Url: https://github.com/boostorg/math/pull/582  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-03-26 14:59:47 UTC  
> Url: https://github.com/boostorg/math/pull/582#issuecomment-808286271  

@jzmaddock This should be good to go. The next PR will be config which will include testing for the standalone functionality.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-26 18:31:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/582#pullrequestreview-622434249  

📁 include/boost/math/special_functions/detail/fp_traits.hpp

```diff
  50 |+ #else
  51 |+ 
  52 |+ #define BOOST_MATH_ENDIAN_BIG_BYTE (__BYTE_ORDER__ == __ORDER_BIG_ENDIAN__)
```

> Username: jzmaddock  
> Created_at: 2021-03-26 18:31:48 UTC  
> Updated_at: 2021-03-26 19:19:49 UTC  
> Url: https://github.com/boostorg/math/pull/582#discussion_r602510392  

I think this is nearly but not quite correct: these macros are GCC specific I assume?  We should probably have another #elif branch here for the case where `__BYTE_ORDER__` is not defined and a reasonably helpful #error message explaining that either the header needs to be fixed, or they should not use standalone mode.

> Username: mborland  
> Created_at: 2021-03-26 18:51:57 UTC  
> Updated_at: 2021-03-26 19:19:49 UTC  
> Url: https://github.com/boostorg/math/pull/582#discussion_r602521617  

Those macros work with Clang as well. Adding an error message if they are not defined makes sense.


---
