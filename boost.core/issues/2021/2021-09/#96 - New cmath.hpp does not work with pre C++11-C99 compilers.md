# #96 - New cmath.hpp does not work with pre C++11/C99 compilers [Closed]

> Username: yachoor  
> Created at: 2021-09-14 11:11:42 UTC  
> Updated at: 2021-09-15 10:33:23 UTC  
> Closed at: 2021-09-15 10:33:23 UTC  
> Url: https://github.com/boostorg/core/issues/96  

I'm using ARMCC 4.1 with hardware vendor provided standard C++ library. My `math.h` and `cmath` headers do not have `isfinite`, `isnan`, `isinf`, etc. functions - they were not in standard before C++11/C99  
The change of dependency in lexical_cast from Boost.Math to core/cmath.hpp in boost 1.76 breaks project compilation as reported in https://github.com/boostorg/lexical_cast/issues/49

---

## Comment 1

> Username: pdimov  
> Created at: 2021-09-14 11:28:22 UTC  
> Url: https://github.com/boostorg/core/issues/96#issuecomment-919062199  

It's hard to find information on ARMCC 4.1. Does it support infinities and NaN at all? It seems that ARMCC 5 has `isinf` and `isnan` in `<math.h>` as part of its C99 support, but I can find no documentation for 4.1, and I've no idea how to implement these functions there.

---

## Comment 2

> Username: yachoor  
> Created at: 2021-09-14 13:04:10 UTC  
> Url: https://github.com/boostorg/core/issues/96#issuecomment-919129656  

The headers and standard library are not taken from compiler toolchain and don't have C99 support - they're provided by hardware vendor with their proprietary operating system.  
The generic implementation from Boost.Math (boost\math\special_functions\fpclassify.hpp) works fine with it. I think it uses the 3rd version:   
>3. If the platform is not C99 compliant, and the binary format for  
>a  floating point type (float, double or long double) can not be determined  
>at compile time, then comparison with std::numeric_limits values  
>is used.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-09-14 16:59:02 UTC  
> Url: https://github.com/boostorg/core/issues/96#issuecomment-919339196  

Should be fixed now, after https://github.com/boostorg/core/commit/8043bafb6b02bb705961e6c3973746050a81c04a and https://github.com/boostorg/core/commit/177c093151c0b8a5b21d4b2e91cff2264dc69788.

---

## Comment 4

> Username: yachoor  
> Created at: 2021-09-15 10:33:23 UTC  
> Url: https://github.com/boostorg/core/issues/96#issuecomment-919901342  

Thank you, this fixes the compilation for me
