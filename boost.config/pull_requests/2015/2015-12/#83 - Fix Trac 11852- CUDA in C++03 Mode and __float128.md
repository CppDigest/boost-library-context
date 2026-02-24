# #83 Fix Trac 11852: CUDA in C++03 Mode and __float128 [Merged]

> Username: ax3l  
> Created at: 2015-12-18 09:31:18 UTC  
> Updated at: 2015-12-18 12:00:40 UTC  
> Merged at: 2015-12-18 11:56:36 UTC  
> Closed at: 2015-12-18 11:56:37 UTC  
> Url: https://github.com/boostorg/config/pull/83  

Fix trac issue  
  https://svn.boost.org/trac/boost/ticket/11852  
  
Similar to  
     https://svn.boost.org/trac/boost/ticket/8048  
  
`__float128` is still unsupported when compiling with nvcc _in non-C++11 mode_ (tested with `CUDA 7.5.18`). First noticed with the latest release (`1.60.0`) and `GCC 4.8.5` but should affect all previous releases depending on used modules.  
  
~~In my case, I triggered it with the components  
  `program_options regex filesystem system thread math_tr1`  
enabled.~~  
  
The reason this is triggered in headers is that `boost 1.60.0` introduced `__float128` type traits in [type_traits/is_floating_point.hpp](https://github.com/boostorg/type_traits/blob/boost-1.60.0/include/boost/type_traits/is_floating_point.hpp#L24-L26) which were not implemented in the previous release [1.59.0](https://github.com/boostorg/type_traits/blob/boost-1.59.0/include/boost/type_traits/is_floating_point.hpp).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-12-18 11:56:19 UTC  
> Url: https://github.com/boostorg/config/pull/83#issuecomment-165762619  

Thanks for looking into this!

---

## Comment 2

> Username: ax3l  
> Created_at: 2015-12-18 12:00:40 UTC  
> Url: https://github.com/boostorg/config/pull/83#issuecomment-165763718  

no problem, thank you!

---
