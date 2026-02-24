# #304 - i278 more macros [Closed]

> Username: ckormanyos  
> Created at: 2020-01-15 21:09:12 UTC  
> Updated at: 2020-01-16 22:10:33 UTC  
> Closed at: 2020-01-16 22:10:33 UTC  
> Url: https://github.com/boostorg/math/issues/304  

The macros in the list below use leading underscore.  
In these macros, should the sub-string "_BOOST" be changed to sth like "BOOST_MATH"?  
(Which then I could do myself if it should be done.)  
  
```  
#ifndef _BOOST_CSTDFLOAT_CMATH_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_cmath.hpp	12	9  
#define _BOOST_CSTDFLOAT_CMATH_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_cmath.hpp	13	9  
#endif // _BOOST_CSTDFLOAT_CMATH_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_cmath.hpp	1093	11  
#ifndef _BOOST_CSTDFLOAT_COMPLEX_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_complex.hpp	12	9  
  #define _BOOST_CSTDFLOAT_COMPLEX_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_complex.hpp	13	11  
#endif // _BOOST_CSTDFLOAT_COMPLEX_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_complex.hpp	38	11  
#ifndef _BOOST_CSTDFLOAT_COMPLEX_STD_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_complex_std.hpp	13	9  
  #define _BOOST_CSTDFLOAT_COMPLEX_STD_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_complex_std.hpp	14	11  
#endif // _BOOST_CSTDFLOAT_COMPLEX_STD_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_complex_std.hpp	641	11  
#ifndef _BOOST_CSTDFLOAT_IOSTREAM_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_iostream.hpp	12	9  
  #define _BOOST_CSTDFLOAT_IOSTREAM_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_iostream.hpp	13	11  
#endif // _BOOST_CSTDFLOAT_IOSTREAM_2014_02_15_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_iostream.hpp	774	11  
#ifndef _BOOST_CSTDFLOAT_LIMITS_2014_01_09_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_limits.hpp	12	9  
  #define _BOOST_CSTDFLOAT_LIMITS_2014_01_09_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_limits.hpp	13	11  
#endif // _BOOST_CSTDFLOAT_LIMITS_2014_01_09_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_limits.hpp	85	11  
#ifndef _BOOST_CSTDFLOAT_TYPES_2014_01_09_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_types.hpp	12	9  
  #define _BOOST_CSTDFLOAT_TYPES_2014_01_09_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_types.hpp	13	11  
#endif // _BOOST_CSTDFLOAT_BASE_TYPES_2014_01_09_HPP_	.\boost_1_72_0\boost\math\cstdfloat\cstdfloat_types.hpp	441	11  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-01-15 21:16:49 UTC  
> Url: https://github.com/boostorg/math/issues/304#issuecomment-574859917  

Aren't a lot of these macros around dead code?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2020-01-15 21:32:11 UTC  
> Url: https://github.com/boostorg/math/issues/304#issuecomment-574865744  

> Aren't a lot of these macros around dead code?  
  
Good question.  
I don't really know if anyone is using cstdfloat?  
A couple test files were using them in the Boost distro.  
But I do not know if any of world is using them

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-01-16 22:10:33 UTC  
> Url: https://github.com/boostorg/math/issues/304#issuecomment-575370601  

Closed with PR #305
