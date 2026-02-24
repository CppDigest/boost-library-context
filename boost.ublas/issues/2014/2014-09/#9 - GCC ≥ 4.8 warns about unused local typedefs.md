# #9 - GCC ≥ 4.8 warns about unused local typedefs [Closed]

> Username: the-nic  
> Created at: 2014-09-18 12:43:32 UTC  
> Updated at: 2026-01-06 12:56:32 UTC  
> Closed at: 2026-01-06 12:56:32 UTC  
> Url: https://github.com/boostorg/ublas/issues/9  

Using gcc ≥ 4.8 causes a lot of warnings about unused local typedefs to be emitted, eg. (there may be more):  
  
```  
include/boost/numeric/ublas/triangular.hpp:1862:46: warning: typedef ‘difference_type’ locally defined but not used [-Wunused-local-typedefs]  
include/boost/numeric/ublas/detail/matrix_assign.hpp:969:40: warning: typedef ‘value_type’ locally defined but not used [-Wunused-local-typedefs]  
include/boost/numeric/ublas/detail/matrix_assign.hpp:1280:39: warning: typedef ‘size_type’ locally defined but not used [-Wunused-local-typedefs]  
```  
  
..and so on. This causes a lot of noise in compiler output

---

## Comment 1

> Username: nasosi  
> Created at: 2014-09-29 14:22:11 UTC  
> Url: https://github.com/boostorg/ublas/issues/9#issuecomment-57167773  

Many of those have been dealt with but cannot be all catched by the unit tests. If after release 1.57 some of them still remain please repost them so we can get rid of them.  
  
Thanks!

---

## Comment 2

> Username: cmakassikis  
> Created at: 2014-11-25 13:17:55 UTC  
> Url: https://github.com/boostorg/ublas/issues/9#issuecomment-64397822  

Hereafter, two warnings which have remained (MinGW gcc 4.9.1):  
  
```  
boost_1_57_0/include/boost/numeric/ublas/detail/matrix_assign.hpp:1262:39: warning: typedef 'size_type' locally defined but not used [-Wunused-local-typedefs]  
boost_1_57_0/include/boost/numeric/ublas/detail/matrix_assign.hpp:1288:39: warning: typedef 'size_type' locally defined but not used [-Wunused-local-typedefs]  
```  
  
Kind Regards,

---

## Comment 3

> Username: nasosi  
> Created at: 2014-12-15 21:04:41 UTC  
> Url: https://github.com/boostorg/ublas/issues/9#issuecomment-67065413  

Thanks for reporting!  
  
This shouldn't be a problem because the typedef is used right below but gcc has a different opinion. Anyway to avoid it I pushed a fix in the develop branch.
