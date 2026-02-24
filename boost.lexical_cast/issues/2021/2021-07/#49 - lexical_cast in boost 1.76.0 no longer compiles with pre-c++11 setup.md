# #49 - lexical_cast in boost 1.76.0 no longer compiles with pre-c++11 setup [Closed]

> Username: yachoor  
> Created at: 2021-07-23 11:50:05 UTC  
> Updated at: 2021-09-12 06:14:24 UTC  
> Closed at: 2021-09-12 06:14:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/49  

I'm using old ARMCC 4.1 compiler with vendor provided standard C++ library :(  
My cmath / math.h do not have C++11/C99 floating point functions.  
I did not see any mention about requiring C++11 now or before  
  
```  
"C:\devtools\boost\boost/core/cmath.hpp", line 141: Error:  #135: namespace "std" has no member "isfinite"  
  using std::isfinite;  
             ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 142: Error:  #135: namespace "std" has no member "isnan"  
  using std::isnan;  
             ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 143: Error:  #135: namespace "std" has no member "isinf"  
  using std::isinf;  
             ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 144: Error:  #135: namespace "std" has no member "isnormal"  
  using std::isnormal;  
             ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 145: Error:  #135: namespace "std" has no member "fpclassify"  
  using std::fpclassify;  
             ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 147: Error:  #20: identifier "FP_ZERO" is undefined  
  int const fp_zero = FP_ZERO;  
                      ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 148: Error:  #20: identifier "FP_SUBNORMAL" is undefined  
  int const fp_subnormal = FP_SUBNORMAL;  
                           ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 149: Error:  #20: identifier "FP_NORMAL" is undefined  
  int const fp_normal = FP_NORMAL;  
                        ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 150: Error:  #20: identifier "FP_INFINITE" is undefined  
  int const fp_infinite = FP_INFINITE;  
                          ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 151: Error:  #20: identifier "FP_NAN" is undefined  
  int const fp_nan = FP_NAN;  
                     ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 153: Error:  #135: namespace "std" has no member "signbit"  
  using std::signbit;  
             ^  
"C:\devtools\boost\boost/core/cmath.hpp", line 161: Error:  #135: namespace "std" has no member "copysign"  
      return std::copysign( x, y );  
                  ^  
"C:\devtools\boost\boost/lexical_cast/detail/inf_nan.hpp", line 108: Error:  #135: namespace "boost::core" has no member "isnan"  
              if (boost::core::isnan(value)) {  
                               ^  
"C:\devtools\boost\boost/lexical_cast/detail/inf_nan.hpp", line 109: Error:  #135: namespace "boost::core" has no member "signbit"  
                  if (boost::core::signbit(value)) {  
                                   ^  
"C:\devtools\boost\boost/lexical_cast/detail/inf_nan.hpp", line 117: Error:  #135: namespace "boost::core" has no member "isinf"  
              } else if (boost::core::isinf(value)) {  
                                      ^  
"C:\devtools\boost\boost/lexical_cast/detail/inf_nan.hpp", line 118: Error:  #135: namespace "boost::core" has no member "signbit"  
                  if (boost::core::signbit(value)) {  
                                   ^  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-09-12 06:14:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/49#issuecomment-917565253  

Checked on develop branch, everything works fine on pre c++11 modes https://github.com/boostorg/lexical_cast/actions/runs/1224191260  
  
Probably the issues was already fixed or you have some exotic platform that we were not testing.   
  
@yachoor please, try to reproduce the issue on develop or 1.77 version of the Boost. If it still exist, please report the issue into the https://github.com/boostorg/core and give some hints on how to identify your platform (compiler and standard libraries)
