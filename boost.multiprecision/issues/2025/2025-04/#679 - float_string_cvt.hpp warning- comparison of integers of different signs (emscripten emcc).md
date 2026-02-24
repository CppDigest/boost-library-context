# #679 - float_string_cvt.hpp warning: comparison of integers of different signs (emscripten emcc) [Closed]

> Username: rghosh0  
> Created at: 2025-04-24 18:27:11 UTC  
> Updated at: 2025-04-25 07:38:48 UTC  
> Closed at: 2025-04-25 07:27:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679  

Using em++ / emcc 3.1.66 we get this warning using boost 1.87:  
  
```  
external/boost/libs/multiprecision/include\boost/multiprecision/detail/float_string_cvt.hpp:142:30: warning: comparison of integers of different signs: 'unsigned int' and 'std::streamsize' (aka 'long') [-Wsign-compare]  
  142 |       for (unsigned i = 0; i < digits; ++i)  
      |                            ~ ^ ~~~~~~  
```  
  
(That file hasn't been modified since boost 1.87 which uses multiprecision 7e847d4 )

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-04-24 19:14:50 UTC  
> Updated at: 2025-04-24 19:16:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2828625198  

Hi @rghosh0 over time, we have been handling many warnings like these on msvc/gcc/clang - on gcc/clang involving warnings like `-Wconversion` and `-Wsign-convesion`.  
  
The issue you report seems like a sign conversion (implicit) warning. So a cast will fix it since `std::streamsize` is a signed integral type and it is implicitly being cast to unsigned in the sequence.  
  
I'll fix it with a cast, but I'd also ask you at this time to hammer it for more such warnings. I did a bunch of checks on develop recently and I can't find (much) more with problems on implicit conversion and/or implicit sign-conversion.  
  
So if you find any more, this is a great time to report any you can detect.  
  
I'll fix and you search for more if you can find any.  
  
Cc: @mborland and @jzmaddock

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-04-24 19:21:53 UTC  
> Updated at: 2025-04-24 19:22:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2828640004  

See also #681.  
  
> I'll fix it with a cast  
  
I opted to change the type of the loop-index `i` to `std::streamsize` instead of an `unsigned`cast since this repair form seemed more consistent with the style of the existing code.

---

## Comment 3

> Username: rghosh0  
> Created at: 2025-04-24 20:06:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2828736509  

Thanks! I included every header in multiprecision except the ones that caused errors  
(complex128.hpp, detail/functions/constants.hpp, detail/functions/pow.hpp, detail/functions/trig.hpp, eigen.hpp, float128.hpp, gmp.hpp, mpc.hpp, mpfi.hpp, mpfr.hpp, random.hpp, tommath.hpp)  
  
And there were no other warnings using emscripten.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-04-24 20:13:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2828750689  

> Thanks! I included every header in multiprecision  
  
Thank you @rghosh0  
  
> ... except the ones that caused errors  
(complex128.hpp, detail/functions/constants.hpp, detail/functions/pow.hpp, detail/functions/trig.hpp, eigen.hpp, float128.hpp, gmp.hpp, mpc.hpp, mpfi.hpp, mpfr.hpp, random.hpp, tommath.hpp)  
  
Hmmm... Causing errors seems like something that begs for explanation. Did you encounter any errors that seemed to need repairing? Old trusted files like `detail/functions/pow.hpp` should be error-free. I'd be interested to find out what you are encountering. What kinds of errors do you mean?  
  
> And there were no other warnings using emscripten.  
  
Cool. I will let this easy repair cycle through CI and then if anything else remains, we cna handle that in follow-up issues.  
  
Thx again.  
  
Cc: @jzmaddock and @mborland

---

## Comment 5

> Username: rghosh0  
> Created at: 2025-04-24 22:16:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2828982747  

Sure, here are a few examples including pow.hpp.  Hopefully I can help fix these  
  
#include <boost/multiprecision/complex128.hpp>  
  
```  
In file included from C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/complex128.hpp:9:  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/float128.hpp:15:2: error: libquadmath only works on on i386, x86_64, IA-64, and hppa HP-UX, as well as on PowerPC GNU/Linux targets that enable the vector scalar (VSX) instruction set.  
   15 | #error libquadmath only works on on i386, x86_64, IA-64, and hppa HP-UX, as well as on PowerPC GNU/Linux targets that enable the vector scalar (VSX) instruction set.  
      |  ^  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/float128.hpp:54:10: fatal error: 'quadmath.h' file not found  
   54 | #include <quadmath.h>  
      |          ^~~~~~~~~~~~  
```  
  
  
  
#include <boost/multiprecision/detail/functions/constants.hpp>  
  
```  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/detail/functions/constants.hpp:224:58: error: use of undeclared identifier 'number'  
  224 |    if ((digits != boost::multiprecision::detail::digits2<number<T> >::value()))  
      |                                                          ^  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/detail/functions/constants.hpp:227:74: error: use of undeclared identifier 'et_on'  
  227 |       calc_log2(result, boost::multiprecision::detail::digits2<number<T, et_on> >::value());  
      |                                                                          ^  
(repeats of these on various lines)  
```  
  
#include <boost/multiprecision/detail/functions/pow.hpp>  
  
```  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/detail/functions/pow.hpp:111:64: error: use of undeclared identifier 'et_on'  
  111 |    long tol = boost::multiprecision::detail::digits2<number<T, et_on> >::value();  
      |    
  
(same for various lines...)  
  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/detail/functions/pow.hpp:199:18: error: no template named 'number_category'; did you mean 'boost::multiprecision::number_category'?  
  199 |    static_assert(number_category<T>::value == number_kind_floating_point, "The exp function is only valid for floating point types.");  
      |                  ^~~~~~~~~~~~~~~  
      |                  boost::multiprecision::number_category  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/detail/number_base.hpp:1529:8: note: 'boost::multiprecision::number_category' declared here  
 1529 | struct number_category : public number_category_base<Num, std::is_class<Num>::value || boost::multiprecision::detail::is_arithmetic<Num>::value, std::is_abstract<Num>::value>  
      |        ^  
```

---

## Comment 6

> Username: rghosh0  
> Created at: 2025-04-24 22:34:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2829005935  

Maybe I'm not supposed to include those files directly.  For example in pow.hpp I see  
  
`// This file has no include guards or namespaces - it's expanded inline inside default_ops.hpp`  
  
And in default_ops.hpp I see  
  
```  
//  
// These functions are implemented in separate files, but expanded inline here,  
// DO NOT CHANGE THE ORDER OF THESE INCLUDES:  
//  
#include <boost/multiprecision/detail/functions/constants.hpp>  
#include <boost/multiprecision/detail/functions/pow.hpp>  
#include <boost/multiprecision/detail/functions/trig.hpp>  
```

---

## Comment 7

> Username: rghosh0  
> Created at: 2025-04-25 01:12:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2829164270  

I went back to including every header that doesn't cause an error, and checked the import hierarchy of the remaining files.  The three functions files (constants, pow, trig) mentioned above are indirectly included, so those don't cause an error or warning.  None of the other files listed as causing errors were indirectly included.  
  
There was also one warning I missed (no error with this file)  
  
#include <boost/multiprecision/random.hpp>  
  
```  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/random.hpp:14:9: warning: NOTE: Use of this header (boost/multiprecision/random.hpp) is deprecated: please use the random number library headers directly. [-W#pragma-messages]  
   14 | #pragma message("NOTE: Use of this header (boost/multiprecision/random.hpp) is deprecated: please use the random number library headers directly.")  
      |         ^  
```  
  
For the remaining headers:  
  
#include <boost/multiprecision/eigen.hpp>  
  
```  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/eigen.hpp:10:10: fatal error: 'Eigen/Core' file not found  
   10 | #include <Eigen/Core>  
      |          ^~~~~~~~~~~~  
```  
  
#include <boost/multiprecision/gmp.hpp>  
  
```  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/gmp.hpp:54:10: fatal error: 'gmp.h' file not found  
   54 | #include <gmp.h>  
      |          ^~~~~~~  
```  
  
#include <boost/multiprecision/mpc.hpp>  
  
```  
In file included from C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/mpc.hpp:16:  
In file included from C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/mpfr.hpp:13:  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/gmp.hpp:54:10: fatal error: 'gmp.h' file not found  
   54 | #include <gmp.h>  
      |          ^~~~~~~  
```  
  
#include <boost/multiprecision/mpfi.hpp>  
  
```  
In file included from C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/mpfi.hpp:21:  
In file included from C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/mpfr.hpp:13:  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/gmp.hpp:54:10: fatal error: 'gmp.h' file not found  
   54 | #include <gmp.h>  
      |          ^~~~~~~  
```  
  
#include <boost/multiprecision/tommath.hpp>  
  
```  
C:/src/my-project/external/boost/libs/multiprecision/include\boost/multiprecision/tommath.hpp:20:10: fatal error: 'tommath.h' file not found  
   20 | #include <tommath.h>  
      |          ^~~~~~~~~~~  
```

---

## Comment 8

> Username: ckormanyos  
> Created at: 2025-04-25 07:36:31 UTC  
> Updated at: 2025-04-25 07:38:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/679#issuecomment-2829611542  

Hi @rghosh0 thank you for the detailed and dedicated work with the warnings.  
  
It all looks good to me. I can accept the pragma warning regarding the deprecation of our local random number generators. So we need to leave that one.  
  
The errors get down to the core design of Multiprecision. When @jzmaddock and I invented Boost.Multiprecision about 15 years ago, we used a strictly specified front-end/back-end design. The front end comes through the `number` class.  
  
At that time, and over the years, we included a variety of Boost-licensed as well FOSS integral and floating-point backends. One famous one is the wrap (in Multiprecision) of `gmp_float` which uses GMP as a Multiprecision `number`. The trick is, in order to use `gmp_float`, you need to have GMP installed (or built) on your system. So there is no way around this error and you can't use this great class until you get a GMP. This is super easy on `*nix` and MacOS, but not so easy on other OSes like `Win*`.  
  
Ditto the same with MPC and MPFR and TomMath. You need those packages in order to use the wrapped classes. You also encounter problems using `float128` if your system does not have compiler support for the `__float128` built-in type.  
  
Out of the box, everyone can use `cpp_dec_float`, `cpp_bin_float`, `cpp_int` and the rational number classes. And the complex adapter works with both bin-float as well as dec-float. So you can have lots of fun with all this stuff on any compiler. I even put multiprecision on some high-performance bare-metal embedded systems for a really fun cross development.  
  
Cc: @jzmaddock and @mborland
