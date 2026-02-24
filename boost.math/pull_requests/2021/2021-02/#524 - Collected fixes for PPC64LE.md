# #524 Collected fixes for PPC64LE [Merged]

> Username: jzmaddock  
> Created at: 2021-02-08 19:38:45 UTC  
> Updated at: 2021-08-06 00:27:39 UTC  
> Merged at: 2021-02-09 15:07:52 UTC  
> Closed at: 2021-02-09 15:07:53 UTC  
> Url: https://github.com/boostorg/math/pull/524  

This PR get's things *almost* green when running the tests on PPC64LE.  
  
I have one remaining failure from autodiff which I don't understand: https://github.com/jzmaddock/math/runs/1856451176?check_suite_focus=true  
  
Other than that most of the changes involve fixing the tests to not test long double when BOOST_MATH_NO_LONG_DOUBLE_FUNCTIONS is defined.  
  
Test suites covered: special_fun, distribution_tests, misc, quadrature, interpolators, autodiff  
  
Not currently tested/covered: mp, float128_tests, long-running-tests, examples, tools.  Mostly due to time and space constraints as these are run on the QEMU interpolator.  Adding these would be nice to have, but the core of the library is now functional and tested.  
  
This is a partial fix for https://github.com/boostorg/math/issues/510.  
  
Things discovered about this platform:  
  
* `long double` is the non-contiguous "double double" type which we have never supported as it's basically impossible to reason about.  It's probably possible to get quite a bit working with enough work, but the type continues to surprise at every turn as it's *very* non IEEE conforming.  
* `long double` is not usable in constexpr (even though the software emulated __float128 is).  
* Mixing long double and __float128 in the same expression is a big no no that results in compiler errors.  
  
For the benefit of future googlers, here's the math_info output:  
  
```  
Macros from <math.h>  
    HUGE_VAL                                =inf  
    HUGE_VALF                               =inf  
    HUGE_VALL                               =inf  
    INFINITY                                =inf  
    NAN                                     =(__builtin_nanf (""))  
    FP_INFINITE                             =1  
    FP_NAN                                  =0  
    FP_NORMAL                               =4  
    FP_SUBNORMAL                            =3  
    FP_ZERO                                 =2  
    FP_FAST_FMA                             =1  
    FP_FAST_FMAF                            =1  
    FP_ILOGB0                               =(-2147483647)  
    FP_ILOGBNAN                             =2147483647  
    MATH_ERRNO                              =1  
    MATH_ERREXCEPT                          =2  
    FLT_MIN_10_EXP                          =-37  
    FLT_DIG                                 =6  
    FLT_MIN_EXP                             =-125  
    FLT_EPSILON                             =1.1920929e-07  
    FLT_RADIX                               =2  
    FLT_MANT_DIG                            =24  
    FLT_ROUNDS                              =1  
    FLT_MAX                                 =3.4028235e+38  
    FLT_MAX_10_EXP                          =38  
    FLT_MAX_EXP                             =128  
    FLT_MIN                                 =1.1754944e-38  
    DBL_DIG                                 =15  
    DBL_MIN_EXP                             =-1021  
    DBL_EPSILON                             =2.2204460492503131e-16  
    DBL_MANT_DIG                            =53  
    DBL_MAX                                 =1.7976931348623157e+308  
    DBL_MIN                                 =2.2250738585072014e-308  
    DBL_MAX_10_EXP                          =308  
    DBL_MAX_EXP                             =1024  
    DBL_MIN_10_EXP                          =-307  
    LDBL_MAX_10_EXP                         =308  
    LDBL_MAX_EXP                            =1024  
    LDBL_MIN                                =2.00416836000897277799610805135016e-292  
    LDBL_MIN_10_EXP                         =-291  
    LDBL_DIG                                =31  
    LDBL_MIN_EXP                            =-968  
    LDBL_EPSILON                            =4.94065645841246544176568792868221e-324  
    LDBL_MANT_DIG                           =106  
    LDBL_MAX                                =1.79769313486231580793728971405301e+308  
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
std::numeric_limits information for type float  
    is_specialized       = 1  
    min()                = 1.1754944e-38  
    max()                = 3.4028235e+38  
    digits               = 24  
    digits10             = 6  
    is_signed            = 1  
    is_integer           = 0  
    is_exact             = 0  
    radix                = 2  
    epsilon()            = 1.1920929e-07  
    round_error()        = 0.5  
    min_exponent         = -125  
    min_exponent10       = -37  
    max_exponent         = 128  
    max_exponent10       = 38  
    has_infinity         = 1  
    has_quiet_NaN        = 1  
    has_signaling_NaN    = 1  
    has_denorm           = 1  
    has_denorm_loss      = 0  
    infinity()           = inf  
    quiet_NaN()          = nan  
    signaling_NaN()      = nan  
    denorm_min()         = 1.4012985e-45  
    is_iec559            = 1  
    is_bounded           = 1  
    is_modulo            = 0  
    traps                = 0  
    tinyness_before      = 0  
    round_style          = 1  
  
Epsilon has sane value of std::pow(std::numeric_limits<T>::radix, 1-std::numeric_limits<T>::digits).  
  
    sizeof(float) = 4  
    alignment_of<float> = 4  
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
std::numeric_limits information for type double  
    is_specialized       = 1  
    min()                = 2.2250738585072014e-308  
    max()                = 1.7976931348623157e+308  
    digits               = 53  
    digits10             = 15  
    is_signed            = 1  
    is_integer           = 0  
    is_exact             = 0  
    radix                = 2  
    epsilon()            = 2.2204460492503131e-16  
    round_error()        = 0.5  
    min_exponent         = -1021  
    min_exponent10       = -307  
    max_exponent         = 1024  
    max_exponent10       = 308  
    has_infinity         = 1  
    has_quiet_NaN        = 1  
    has_signaling_NaN    = 1  
    has_denorm           = 1  
    has_denorm_loss      = 0  
    infinity()           = inf  
    quiet_NaN()          = nan  
    signaling_NaN()      = nan  
    denorm_min()         = 4.9406564584124654e-324  
    is_iec559            = 1  
    is_bounded           = 1  
    is_modulo            = 0  
    traps                = 0  
    tinyness_before      = 0  
    round_style          = 1  
  
Epsilon has sane value of std::pow(std::numeric_limits<T>::radix, 1-std::numeric_limits<T>::digits).  
  
    sizeof(double) = 8  
    alignment_of<double> = 8  
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
std::numeric_limits information for type long double  
    is_specialized       = 1  
    min()                = 2.00416836000897277799610805135016e-292  
    max()                = 1.79769313486231580793728971405301e+308  
    digits               = 106  
    digits10             = 31  
    is_signed            = 1  
    is_integer           = 0  
    is_exact             = 0  
    radix                = 2  
    epsilon()            = 4.94065645841246544176568792868221e-324  
    round_error()        = 0.5  
    min_exponent         = -968  
    min_exponent10       = -291  
    max_exponent         = 1024  
    max_exponent10       = 308  
    has_infinity         = 1  
    has_quiet_NaN        = 1  
    has_signaling_NaN    = 1  
    has_denorm           = 1  
    has_denorm_loss      = 0  
    infinity()           = inf  
    quiet_NaN()          = nan  
    signaling_NaN()      = nan  
    denorm_min()         = 4.94065645841246544176568792868221e-324  
    is_iec559            = 1  
    is_bounded           = 1  
    is_modulo            = 0  
    traps                = 0  
    tinyness_before      = 0  
    round_style          = 1  
  
CAUTION: epsilon does not have a sane value.  
  
    sizeof(long double) = 16  
    alignment_of<long double> = 16  
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
Math function overload information for type float  
The Math functions are overloaded for type float  
std::fabs looks OK for type float  
std::abs looks OK for type float  
std::sqrt looks OK for type float  
std::atan2 looks OK for type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
Math function overload information for type double  
The Math functions are overloaded for type double  
std::fabs looks OK for type double  
std::abs looks OK for type double  
std::sqrt looks OK for type double  
std::atan2 looks OK for type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
Math function overload information for type long double  
The Math functions are overloaded for type long double  
std::fabs looks OK for type long double  
std::abs looks OK for type long double  
std::sqrt looks OK for type long double  
std::atan2 looks OK for type long double  
```

---

## Comment 1

> Username: jwakely  
> Created_at: 2021-08-05 20:51:39 UTC  
> Url: https://github.com/boostorg/math/pull/524#issuecomment-893786648  

It seems that for boost 1.76.0 `libboost_math_tr1l.so.1.75.0` and `libboost_math_c99l.so.1.75.0` are no longer built on ppc64le, which breaks the Fedora build. Is that expected? Was it caused by this change?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-08-05 21:54:12 UTC  
> Url: https://github.com/boostorg/math/pull/524#issuecomment-893832628  

>It seems that for boost 1.76.0 libboost_math_tr1l.so.1.75.0 and libboost_math_c99l.so.1.75.0 are no longer built on ppc64le, which breaks the Fedora build. Is that expected? Was it caused by this change?  
  
No, there are no changes to the build system here, and none anywhere I can think of that would cause this.  
  
If you cd into libs/math/build and run ../../../b2 what output do you see?

---

## Comment 3

> Username: rodgert  
> Created_at: 2021-08-06 00:27:39 UTC  
> Url: https://github.com/boostorg/math/pull/524#issuecomment-893913533  

For now I'm going to bypass expecting them to be present in ppc64le builds of boost. It sounds like it's dubious whether they are even correct on this target. After I finish with the Boost 1.76.0 upgrade for Fedora 35 I'll try to dig deeper into why they aren't being built.

---
