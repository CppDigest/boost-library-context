# #198 vector: Simplify at_impl [Merged]

> Username: Kojoley  
> Created at: 2018-10-07 12:46:45 UTC  
> Updated at: 2018-10-25 13:13:29 UTC  
> Merged at: 2018-10-25 13:12:39 UTC  
> Closed at: 2018-10-25 13:12:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/198  

The trick checked for working on GCC 4.7+ and Clang 3.0+.  
  
I am wondering why C++11 things use `BOOST_CONSTEXPR` instead of plain `constexpr`?

---

## Comment 1

> Username: Flast  
> Created_at: 2018-10-25 13:12:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/198#issuecomment-433044863  

> I am wondering why C++11 things use `BOOST_CONSTEXPR` instead of plain  `constexpr` ?  
  
Because it doesn't require constexpr.  
https://github.com/boostorg/fusion/blob/309a2d623f7b7ec21bea3d76653fb2ab1a23a4d5/include/boost/fusion/container/vector/detail/config.hpp#L14-L18  
  
Anyway, the change seems good to me.

---
