# #569 - Compiling float128 failed in g++ 13.2 with the latest boost::multiprecision [Closed]

> Username: seanxnie  
> Created at: 2023-10-30 06:29:56 UTC  
> Updated at: 2024-09-22 12:16:21 UTC  
> Closed at: 2024-09-22 12:16:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569  

Dear all. I just encountered an issue when compiling the following code, which is to invoke the `boost::math::expm1`  function.  
```  
#include <boost/multiprecision/float128.hpp>  
#include <boost/math/constants/constants.hpp>  
#include <boost/math/special_functions/bessel.hpp>  
//#include <boost/multiprecision/mpfr.hpp>  
  
using T = boost::multiprecision::float128;  
//using T = boost::multiprecision::mpfr_float_50;  
  
int main() {  
  
    T m_pi = boost::math::constants::pi<T>();  
    T term = boost::math::expm1(m_pi); // compiling error  
}  
```  
The command line for compiling code is:  
```  
 /usr/bin/g++ -std=c++20 -Iinclude/ -lquadmath -lmpfr -g -Wall expm1test.cpp -o expm1test  
```  
The compiler error output is considerable. However, the main issue is `max min` type conversion:  
```  
expm1test.cpp:12:32:   required from here  
include/boost/multiprecision/float128.hpp:162:66: error: could not convert ‘boost::multiprecision::quad_constants::quad_max’ from ‘const float128_type’ {aka ‘const __float128’} to ‘std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >::number_type’ {aka ‘boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>’}  
  162 | #define BOOST_MP_QUAD_MAX boost::multiprecision::quad_constants::quad_max  
      |                           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~  
      |                                                                  |  
      |                                                                  const float128_type {aka const __float128}  
include/boost/multiprecision/float128.hpp:820:73: notes: in expansion of macro ‘BOOST_MP_QUAD_MAX’  
  820 |    static BOOST_MP_CXX14_CONSTEXPR number_type(max)() noexcept { return BOOST_MP_QUAD_MAX; }  
      |                                                                         ^~~~~~~~~~~~~~~~~  
```  
In above code, this issue will disappear if replacing `float128` by `mpfr_float_50`. The `boost::multiprecision` I used is the latest development version. The error happens whenever a math function needs to invoke `max min` of `float128`. It seems that a bug exists in `float128.hpp` in boost. I hope the information above is enough to find the issue. Thanks a lot.  
  
Sean

---

## Comment 1

> Username: mborland  
> Created at: 2023-10-30 07:12:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1784602013  

Does the issue persist if you build with `-std=gnu++20` instead of `-std=c++20`?

---

## Comment 2

> Username: seanxnie  
> Created at: 2023-10-30 07:27:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1784619232  

> Does the issue persist if you build with `-std=gnu++20` instead of `-std=c++20`?  
  
Yes, the issue is solved. This is to enable gnu extensions in c++20. Thank you!

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-10-30 09:07:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1784763862  

Just to confirm, that float128.hpp does indeed require gnu++XX not c++-XX, it should be documented somewhere...

---

## Comment 4

> Username: seanxnie  
> Created at: 2023-10-30 09:38:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1784814966  

> Just to confirm, that float128.hpp does indeed require gnu++XX not c++-XX, it should be documented somewhere...  
  
As far as my findings from boost.org and pdfs, requiring gnu++XX in float128 is not documented.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2023-10-30 11:48:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785019865  

I think you're right actually, I dimly remember removing that restriction, hence the use of quad_min/quad_max rather than Q suffixed constants.  Will investigate further...

---

## Comment 6

> Username: ckormanyos  
> Created at: 2023-10-30 12:07:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785047206  

> hence the use of quad_min/quad_max rather than Q suffixed constants. Will investigate further...  
  
Ummm, I'm not the expert here, but I think what the `-std=gnuXX` language extensions do is add `numeric_limits` support and the quad-suffixes. I'm not sure if anything else is added.  
  
I'm almost certain that the fundamental support of `libquadmath` is available in `c++`-standards.  
  
Check it at [Godbolt](https://godbolt.org/z/vxndvnYcE).

---

## Comment 7

> Username: ckormanyos  
> Created at: 2023-10-30 12:09:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785049900  

Admittedly, `__float128` has been a long-time source of confusion for me personally. And I've made a few mistakes down that road with assumptioins...

---

## Comment 8

> Username: ckormanyos  
> Created at: 2023-10-30 12:16:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785061173  

My example was not nice. [Here](https://godbolt.org/z/ae9cszh8G) is a better one.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2023-10-30 12:55:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785134769  

OK, so here's the issue... we used to rely on Q suffixed constants for numeric_limits support, those were removed so that's all good.  The issue here is convertibility... __float128 is only _explicitly_ convertible to type number when building with c++XX rather than gnu++XX.... and the reason for that is that __float128 is _not_ an arithmetic type (according to <type_traits>) when building with c++XX.... and we have a general rule in place that "arithmetic types are always implicitly convertible, everything else is only explicitly convertible and only if the backend has a suitable constructor".  
  
So either.... we change the convertibility rules so that __float128 is always treated as an arithmetic type, or I just add some explicit casts to the numeric_Limits functions.  The former seems more logical, but messes up the enable_if logic if we have to treat __float128 as a special case.  
  
I also have a hunch that we're not testing any of this, so the testing logic needs looking at too...

---

## Comment 10

> Username: mborland  
> Created at: 2023-10-30 12:59:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785143131  

> OK, so here's the issue... we used to rely on Q suffixed constants for numeric_limits support, those were removed so that's all good. The issue here is convertibility... __float128 is only _explicitly_ convertible to type number when building with c++XX rather than gnu++XX.... and the reason for that is that __float128 is _not_ an arithmetic type (according to <type_traits>) when building with c++XX.... and we have a general rule in place that "arithmetic types are always implicitly convertible, everything else is only explicitly convertible and only if the backend has a suitable constructor".  
>   
> So either.... we change the convertibility rules so that __float128 is always treated as an arithmetic type, or I just add some explicit casts to the numeric_Limits functions. The former seems more logical, but messes up the enable_if logic if we have to treat __float128 as a special case.  
>   
> I also have a hunch that we're not testing any of this, so the testing logic needs looking at too...  
  
As a note the specializations of numeric_limits is changing in GCC 14: https://github.com/boostorg/math/issues/992. I am not sure off hand if that is the case for <type_traits> too.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2023-10-30 13:03:42 UTC  
> Updated at: 2023-10-30 13:04:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785150710  

> `__float128` is _not_ an arithmetic type  
  
Oh yes. That was one of the other things that is enabled by `-std=gnu++XX` as opposed to `-std=c++XX`. The former (GNU)  also supports `__float128` as a built-in type in the sense of `<type_traits>`.  
  
We need a list somewhere reminding what _does_ and _does_ _not_ happen to `__float128` in GNU/non-GNU.

---

## Comment 12

> Username: ckormanyos  
> Created at: 2023-10-30 13:54:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/569#issuecomment-1785253864  

This [program](https://godbolt.org/z/P9oncTdPf) showcases what is available for `__float128` when using `-std=gnu++XX`.
