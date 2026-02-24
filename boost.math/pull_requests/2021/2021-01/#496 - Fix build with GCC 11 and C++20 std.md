# #496 Fix build with GCC 11 and C++20 std [Closed]

> Username: stac47  
> Created at: 2021-01-21 17:38:31 UTC  
> Updated at: 2021-01-21 18:42:31 UTC  
> Closed at: 2021-01-21 18:42:30 UTC  
> Url: https://github.com/boostorg/math/pull/496  

Hello,  
  
From C++20, ADL can find a unqualified function template:  
    https://en.cppreference.com/w/cpp/language/adl  
  
For the time being, GCC11 (developement branch) raise an error:  
  
```  
./boost/math/special_functions/detail/round_fwd.hpp:89:23: error: 'namespace boost::math { }::modf' conflicts with a previous declaration  
   89 |    using boost::math::modf;  
      |                       ^~~~  
./boost/math/policies/error_handling.hpp:703:4: note: in expansion of macro 'BOOST_MATH_STD_USING'  
  703 |    BOOST_MATH_STD_USING  
      |    ^~~~~~~~~~~~~~~~~~~~  
In file included from ./boost/config/no_tr1/cmath.hpp:21,  
                 from ./boost/math/tools/config.hpp:19,  
                 from ./boost/math/tools/promotion.hpp:26,  
                 from ./boost/math/special_functions/detail/round_fwd.hpp:12,  
                 from ./boost/math/special_functions/math_fwd.hpp:27,  
                 from ./boost/math/special_functions/airy.hpp:11,  
                 from ./boost/math/special_functions.hpp:15,  
                 from libs/math/build/../src/tr1/pch.hpp:9:  
/opt/1A/toolchain/x86_64-v21.0.15/include/c++/11.0.0/cmath:380:3: note: previous declaration 'namespace std { }::modf'  
  380 |   modf(long double __x, long double* __iptr)  
      |   ^~~~  
```  
Note: this is probably a GCC11 bug but it is worth marking the useless code in C++20 standard.  
Don't hesitate to reject this PR.  
Regards,  
Laurent

---

## Comment 1

> Username: stac47  
> Created_at: 2021-01-21 18:42:30 UTC  
> Url: https://github.com/boostorg/math/pull/496#issuecomment-764856420  

Wrong PR.

---
