# #510 - CI Failure with Bessel [Closed]

> Username: mborland  
> Created at: 2021-02-02 16:35:51 UTC  
> Updated at: 2023-04-07 12:39:54 UTC  
> Closed at: 2023-04-06 12:16:47 UTC  
> Url: https://github.com/boostorg/math/issues/510  

Several test failures ([example](https://travis-ci.org/github/mborland/math/jobs/756971704#L6403)) on `cyl_bessel_k`, `cyl_bessel_j_prime`, `cyl_neumann_prime`, `cyl_bessel_i_prime`, `beta`, `cyl_bessel_k_prime` all on GCC-9 CXXSTD=17 and ARCH=PPC64LE.

---

## Comment 1

> Username: mborland  
> Created at: 2021-02-02 17:16:46 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-771807149  

Similar errors on [ARCH=S390X ](https://travis-ci.org/github/mborland/math/jobs/756971718#L1725)

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-05 19:38:21 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-774245805  

This error:  
  
```  
Testing Bessel Kn: Mathworld Data with type long double  
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
cyl_bessel_k<long double> Max = 3.385e+13 RMS Mean=1.128e+13  
  
    worst case at row: 8  
  
    { -1000, 700, 6.51561979144735818903553852606382e-31 }  
```  
  
Is rather suspiciously close to truncated-to-double-somewhere-precision.  
  
However, the code is super-simple and needs only exp/sqrt from the std lib.  
  
I'll try and figure out how to debug some of this stuff locally.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-02-07 08:33:32 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-774634802  

OK progress of sorts.  
  
I found this GHA that will run jobs on the QEMU emulator: https://github.com/uraimo/run-on-arch-action and managed to get a basic test running https://github.com/jzmaddock/math/runs/1846141199?check_suite_focus=true.  
  
Math info output is illuminating:  
  
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
  
So... long double is actually a "double double" with a non-sane epsilon value, an exponent range less than that for double, and potentially a discontinuous mantissa.  It's basically impossible to reason about the behaviour of these types (from an IEEE perspective) and it break tons of assumptions in the code, so historically we've always dodged this one and set `BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS` in this case.  
  
I'm attempting to run the special fun tests on the emulator now.... I don't necessarily hold out much hope, it's mega slow, even building b2 takes an age, but we'll see.  It would be better to cross compile and then just feed the executables into the emulator - b2 supports that, but it's a script I don't particularly want to have to write ;)

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-02-07 10:08:48 UTC  
> Updated at: 2021-02-07 10:10:28 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-774646985  

> So... long double is actually a "double double" with a non-sane epsilon value, an exponent range less than that for double, and potentially a discontinuous mantissa. It's basically impossible to reason about the behaviour of these types (from an IEEE perspective)   
  
Great work. We might be better off running closer on the metal. I actually do (in real life with a couple of other guys) engage in OS-less debug sessions with real-time emulators on Raspberry Pi (as in use the ELF-file for single-step debugging and the like). Although I've never combined that metal domain with Boost testing. It will end up taking me (and will take me) a while to get around to it, such a long while that it will be measured in months or year(s), as I'm busy a lot of times.  
  
I'm interested in finding a compiler/hardware combination having hardware support for 64-bit double. Long double will be a bit of an unknown entity in the embedded world, but at least find one where long double is double makes it predictable.  
  
QEMU might also be expectedly limited in performance on that one in many ways such as taking exorbitantly long times to execute, whereas a real hardware device such as a Cortex-M7 running with just a little bit of connectivity might give a better window into getting real answers on an Azure pipeline. I got a [paper on IoT and Azure for ST devices](https://www.st.com/en/embedded-software/x-cube-azure.html) but have not yet investigated it. I've literally got a crate full of numerous embedded boards, but have not in any way investigated what it means to Boost Stuff on the metal (yet).

---

## Comment 5

> Username: mborland  
> Created at: 2021-02-07 10:09:42 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-774647095  

I know that boost cannot use Travis anymore, but it seems to run fine on a fork. I have been using [this yaml file](https://github.com/mborland/math/blob/develop/.travis.yml). Takes about 5 hours to run. The two remaining timeout errors are from exceeding the log length. Just to get them to finish I was going to modify some of them to abort after ~100 failures.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-02-11 19:26:03 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-777731393  

I'm (slowly) working through the S390X issues now.  This has a genuine 128-bit long double, most of the errors I've found so far are issues with the test programs not being 128-bit clean, but is also one obscure bug in Bessel JY.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2023-04-06 16:09:36 UTC  
> Updated at: 2023-04-06 16:11:17 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-1499306183  

Hi Matt (@mborland) should I test something on ARM bare-metal?  
  
If you'd like, I've learned a bit more about getting QEMU running on GHA. If you'd one day like to set up a tiny program testing a few specfun cases on bare metal in CI I'd like to have that discussion...

---

## Comment 8

> Username: mborland  
> Created at: 2023-04-07 12:02:03 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-1500225589  

I think that would be reasonable if it doesn't use a ton of CI run time. Are the toolchains generally C++14 compliant?

---

## Comment 9

> Username: ckormanyos  
> Created at: 2023-04-07 12:22:18 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-1500242600  

>  that would be reasonable if it doesn't use a ton of CI run time.  
  
I can work up a draft showning how this kind of CI run might "look and feel". At first I would just take one small existing test for 4-byte `float` and see if it can be made to run. I'm not looking to use very much CI time for this kind of thing. You basically have to `apt install` the GCC toolchain `gcc-arm-none-eabi`, then `wget` a qemu distro and run a little `main()`  
  
> Are the toolchains generally C++14 compliant  
  
Yes. The GCC toolchains for embedded systems use the same front end as _regular_ GCCs and feature today's modern compliance. So if I install `gcc-arm-none-eabi` in GCC 10, then you get all the way up to `-std=c++2a`.

---

## Comment 10

> Username: mborland  
> Created at: 2023-04-07 12:25:28 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-1500244749  

Sounds good to me. I think we need to relook dropping MSVC 14.0 because it doesn't handle the deprecated attribute properly: #974. That should buy us some space to add different toolchains.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2023-04-07 12:39:54 UTC  
> Url: https://github.com/boostorg/math/issues/510#issuecomment-1500253610  

Cool Matt.  
  
Oh, I guess I actually already prepared something a few months ago and forgot.  
  
See the [ckormanyos/boost_math_test_qemu](https://github.com/ckormanyos/boost_math_test_qemu) repo and its markdown. The CI runs in GHA.
