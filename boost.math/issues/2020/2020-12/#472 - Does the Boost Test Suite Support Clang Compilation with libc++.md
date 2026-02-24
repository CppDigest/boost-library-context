# #472 - Does the Boost Test Suite Support Clang Compilation with libc++? [Closed]

> Username: linjx123456  
> Created at: 2020-12-26 08:05:47 UTC  
> Updated at: 2020-12-29 08:49:15 UTC  
> Closed at: 2020-12-29 08:49:14 UTC  
> Url: https://github.com/boostorg/math/issues/472  

| ../boost/math/special_functions/gamma.hpp:655:27: error: call to 'log' is ambiguous  
|                         + log(boost::math::constants::pi<T>())  
|                           ^~~  
XXX/recipe-sysroot/usr/include/bits/mathcalls.h:104:17: note: candidate function  
| __MATHCALL_VEC (log,, (_Mdouble_ __x));  
|                 ^  
XXX/usr/include/c++/v1/math.h:953:46: note: candidate function  
| inline _LIBCPP_INLINE_VISIBILITY float       log(float __lcpp_x) _NOEXCEPT       {return ::logf(__lcpp_x);}  
|                                              ^  
XXX/usr/include/c++/v1/math.h:954:46: note: candidate function  
| inline _LIBCPP_INLINE_VISIBILITY long double log(long double __lcpp_x) _NOEXCEPT {return ::logl(__lcpp_x);}

---

## Comment 1

> Username: mclow  
> Created at: 2020-12-26 14:46:01 UTC  
> Url: https://github.com/boostorg/math/issues/472#issuecomment-751362914  

"/usr/include/bits" is not part of libc++

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-12-26 15:49:45 UTC  
> Url: https://github.com/boostorg/math/issues/472#issuecomment-751368408  

Yes but:  
  
* I'm unable to find that line in current Boost, so I assume you're using an old version, and  
* As @mclow says, the conflicting header is not part of libc++, I suspect you have a conflicted / messed up install of something somewhere.  
  
Try compiling:  
  
```  
#include <cmath>  
#include <math.h>  
  
int main()  
{  
   using std::log;  
   double d = 3.14;  
   d = log(d);  
}  
```  
  
If you see the same issue then it's a toolchain install issue, if not then I'm stumped.

---

## Comment 3

> Username: linjx123456  
> Created at: 2020-12-28 06:53:35 UTC  
> Url: https://github.com/boostorg/math/issues/472#issuecomment-751603371  

#include <cmath>  
#include <math.h>  
  
int main()  
{  
   using std::log;  
   double d = 3.14;  
   d = log(d);  
}  
----it‘s ok

---

## Comment 4

> Username: linjx123456  
> Created at: 2020-12-28 06:58:39 UTC  
> Url: https://github.com/boostorg/math/issues/472#issuecomment-751604700  

BOOST_AUTO_TEST_CASE( test_main )  
{  
   BOOST_MATH_CONTROL_FP;  
   test_spots(0.0Q);                  -----------------The error is caused by test_spots(0.0Q); in test_factorials.cpp  
   cout << "max factorial for __float128"  << boost::math::max_factorial<boost::floatmax_t>::value  << endl;  
}  
  
compiling Using the LLVM ,However, the GCC compilation is normal.  
Another issue is test_spots (0.0Q); supported? test_spots (0.0F); is ok

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-12-28 16:40:52 UTC  
> Url: https://github.com/boostorg/math/issues/472#issuecomment-751780007  

What is it you are trying to do - there is no `test_spots(0.0Q);` line in test_factorials.cpp nor should there be.

---

## Comment 6

> Username: linjx123456  
> Created at: 2020-12-29 01:22:36 UTC  
> Url: https://github.com/boostorg/math/issues/472#issuecomment-751913819  

The latest test_factorials.cpp file still contains test_spots(0.0Q).

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-12-29 08:49:14 UTC  
> Url: https://github.com/boostorg/math/issues/472#issuecomment-751999307  

Ah sorry, two different files with that name, if you're looking at libs\math\test\float128\*.cpp then nothing in that folder will build with anything except GCC.
