# #560 - GCC14 libstdc++: multiprecision/number.hpp redefines class std::numeric_limits<__float128> [Closed]

> Username: jamborm  
> Created at: 2023-06-07 13:24:54 UTC  
> Updated at: 2023-06-08 07:09:19 UTC  
> Closed at: 2023-06-08 07:09:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/560  

With (development version) of GCC 14 and its libstdc++ (after commit https://gcc.gnu.org/git/gitweb.cgi?p=gcc.git;h=f150a084e25 ), attempting to compile a file which includes multiprecision/number.hpp such as plain  
  
```  
#include "/home/mjambor/boost/boost/libs/multiprecision/include/boost/multiprecision/number.hpp"  
```  
  
fails with:  
  
```  
mjambor@virgil:/tmp/zzz$ ~/gcc/trunk/inst/bin/g++ -I /home/mjambor/boost/boost t2.cc -S  
In file included from /home/mjambor/boost/boost/boost/cstdfloat.hpp:22,  
                 from /home/mjambor/boost/boost/boost/multiprecision/detail/float128_functions.hpp:15,  
                 from /home/mjambor/boost/boost/boost/multiprecision/detail/fpclassify.hpp:13,  
                 from /home/mjambor/boost/boost/boost/multiprecision/detail/default_ops.hpp:15,  
                 from /home/mjambor/boost/boost/boost/multiprecision/detail/generic_interconvert.hpp:12,  
                 from /home/mjambor/boost/boost/libs/multiprecision/include/boost/multiprecision/number.hpp:12,  
                 from t2.cc:1:  
/home/mjambor/boost/boost/boost/math/cstdfloat/cstdfloat_limits.hpp:46:13: error: redefinition of ‘class std::numeric_limits<__float128>’  
   46 |       class numeric_limits<boost::math::cstdfloat::detail::float_internal128_t>  
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/mjambor/boost/boost/boost/limits.hpp:19,  
                 from /home/mjambor/boost/boost/boost/integer_fwd.hpp:16,  
                 from /home/mjambor/boost/boost/boost/integer.hpp:18,  
                 from /home/mjambor/boost/boost/boost/multiprecision/detail/standalone_config.hpp:52,  
                 from /home/mjambor/boost/boost/libs/multiprecision/include/boost/multiprecision/number.hpp:10:  
/home/mjambor/gcc/trunk/inst/include/c++/14.0.0/limits:2079:12: note: previous definition of ‘class std::numeric_limits<__float128>’  
 2079 |     struct numeric_limits<__float128>  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~  
  
```

---

## Comment 1

> Username: mborland  
> Created at: 2023-06-07 13:28:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/560#issuecomment-1580825926  

Confirmed and reported against Boost.Math here: https://github.com/boostorg/math/issues/992.

---

## Comment 2

> Username: mborland  
> Created at: 2023-06-08 07:09:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/560#issuecomment-1582007174  

Fixed by: https://github.com/boostorg/math/pull/993
