# #364 - Standalone? [Closed]

> Username: NAThompson  
> Created at: 2021-08-31 01:19:33 UTC  
> Updated at: 2022-03-24 15:41:09 UTC  
> Closed at: 2022-03-24 15:41:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364  

Heads up: Just had a use case for boost/multiprecision as a standalone.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-08-31 04:51:40 UTC  
> Updated at: 2021-08-31 04:52:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-908896955  

> boost/multiprecision as a standalone  
  
That is in my opinion a good idea. I'm not very good at that kind of thing, but I would definitely help if I can.  
  
Some of my other projecs use parts of Math and Multiprecision. Prior to C++11 and standalone Math, I needed to get over a hundred submodules, not it's much lower. Standalone Multiprecision would really help me in CI of several of my unrelated-to-Boost projects.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-08-31 08:29:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-909018969  

I think we're getting closer - I have a better rational_adaptor in the works which will remove the dependency to boost.rational.

---

## Comment 3

> Username: mborland  
> Created at: 2021-09-19 07:13:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-922427146  

@NAThompson is it a safe assumption that standalone Multiprecision can depend on standalone Math? Here is the current breakdown of dependencies:  
  
```  
Primary dependencies for multiprecision:  
  
assert:  
    <boost/assert.hpp>  
        from <boost/multiprecision/cpp_int/cpp_int_config.hpp>  
  
config:  
    <boost/config.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
        from <boost/multiprecision/detail/atomic.hpp>  
        from <boost/multiprecision/detail/constexpr.hpp>  
        from <boost/multiprecision/float128.hpp>  
        from <boost/multiprecision/number.hpp>  
        from <boost/multiprecision/traits/explicit_conversion.hpp>  
  
core:  
    <boost/core/empty_value.hpp>  
        from <boost/multiprecision/cpp_int.hpp>  
    <boost/core/no_exceptions_support.hpp>  
        from <boost/multiprecision/detail/default_ops.hpp>  
        from <boost/multiprecision/detail/functions/pow.hpp>  
    <boost/core/nvp.hpp>  
        from <boost/multiprecision/detail/number_base.hpp>  
  
integer:  
    <boost/integer.hpp>  
        from <boost/multiprecision/cpp_int/cpp_int_config.hpp>  
    <boost/integer/common_factor_rt.hpp>  
        from <boost/multiprecision/cpp_int/misc.hpp>  
    <boost/integer/static_min_max.hpp>  
        from <boost/multiprecision/cpp_int.hpp>  
    <boost/integer_traits.hpp>  
        from <boost/multiprecision/cpp_int/cpp_int_config.hpp>  
  
lexical_cast:  
    <boost/lexical_cast.hpp>  
        from <boost/multiprecision/detail/number_base.hpp>  
  
math:  
    <boost/math/constants/constants.hpp>  
        from <boost/multiprecision/mpfi.hpp>  
    <boost/math/policies/error_handling.hpp>  
        from <boost/multiprecision/detail/default_ops.hpp>  
    <boost/math/policies/policy.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
    <boost/math/special_functions/acosh.hpp>  
        from <boost/multiprecision/cpp_bin_float.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
        from <boost/multiprecision/gmp.hpp>  
    <boost/math/special_functions/asinh.hpp>  
        from <boost/multiprecision/cpp_bin_float.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
        from <boost/multiprecision/gmp.hpp>  
    <boost/math/special_functions/atanh.hpp>  
        from <boost/multiprecision/cpp_bin_float.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
        from <boost/multiprecision/gmp.hpp>  
    <boost/math/special_functions/cbrt.hpp>  
        from <boost/multiprecision/cpp_bin_float.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
        from <boost/multiprecision/gmp.hpp>  
    <boost/math/special_functions/expm1.hpp>  
        from <boost/multiprecision/cpp_bin_float.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
        from <boost/multiprecision/gmp.hpp>  
    <boost/math/special_functions/fpclassify.hpp>  
        from <boost/multiprecision/concepts/mp_number_archetypes.hpp>  
        from <boost/multiprecision/detail/default_ops.hpp>  
        from <boost/multiprecision/gmp.hpp>  
        from <boost/multiprecision/mpfi.hpp>  
        from <boost/multiprecision/mpfr.hpp>  
        from <boost/multiprecision/tommath.hpp>  
    <boost/math/special_functions/gamma.hpp>  
        from <boost/multiprecision/cpp_bin_float.hpp>  
        from <boost/multiprecision/cpp_dec_float.hpp>  
        from <boost/multiprecision/gmp.hpp>  
    <boost/math/special_functions/hypot.hpp>  
        from <boost/multiprecision/detail/default_ops.hpp>  
    <boost/math/special_functions/next.hpp>  
        from <boost/multiprecision/detail/default_ops.hpp>  
    <boost/math/special_functions/trunc.hpp>  
        from <boost/multiprecision/cpp_bin_float.hpp>  
    <boost/math/tools/complex.hpp>  
        from <boost/multiprecision/detail/number_base.hpp>  
  
predef:  
    <boost/predef/other/endian.h>  
        from <boost/multiprecision/cpp_int.hpp>  
        from <boost/multiprecision/detail/bitscan.hpp>  
  
random:  
    <boost/random.hpp>  
        from <boost/multiprecision/miller_rabin.hpp>  
        from <boost/multiprecision/random.hpp>  
  
rational:  
    <boost/rational.hpp>  
        from <boost/multiprecision/rational_adaptor.hpp>  
```

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-09-19 07:41:42 UTC  
> Updated at: 2021-09-19 07:42:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-922430283  

I started #369, kind of asynchronously from this discussion. That might not have been the greatest planning from my side. I'm looking into the standalone nature of `cpp_dec_float` and unrelated improvements on constant-correctness in that branch.  
  
Question: Should we get individual backends standalone-ready in various branches? Or would it make more sense to strive toward standalone for the collection of backends (post-rational-adapt) as a complete body of work, as these have similar `number`dependencies?  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 5

> Username: NAThompson  
> Created at: 2021-09-19 18:29:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-922516521  

@mborland : Actually I think that `lexical_cast` will be the hardest right?

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-09-19 18:43:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-922518288  

> think that lexical_cast will be the hardest right?  
  
Good question. I identified 3 used of `lexical_cast` in `cpp_dec_float`. They seem simple enough, and there are a few more uses in other headers. I'm not entirely sure, but it seems like we are primarily casting to/from string/uint, so there could be a few alternatives.  
  
I wanted to ask @jzmaddock how he thought best to deal with `lexical_cast`? Should we make our own? Use `std::itoa`/`atoi`? Personally, i would prefer to avoid string-streaming if possible...

---

## Comment 7

> Username: jzmaddock  
> Created at: 2021-09-20 07:39:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-922697017  

Most of the remaining uses are trivial, and for that we already have https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/detail/itos.hpp, it's just not being used comprehensively yet.  
  
The "hard" use case, is it's used for some generic interconversions between types https://github.com/boostorg/multiprecision/blob/7a04dcdfb69719818b7aaa6386bc505a917799b8/include/boost/multiprecision/detail/default_ops.hpp#L1071.  In the case that `result` has a const char* construct/assign (ie 2 multiprecision classes) we could indeed remove lexical_cast here, otherwise I think we would just have to add a static_assert - "not implemented" message in the standalone use case?

---

## Comment 8

> Username: mborland  
> Created at: 2021-09-20 14:56:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-923004460  

> Most of the remaining uses are trivial, and for that we already have https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/detail/itos.hpp, it's just not being used comprehensively yet.  
>   
> The "hard" use case, is it's used for some generic interconversions between types  
>   
> https://github.com/boostorg/multiprecision/blob/7a04dcdfb69719818b7aaa6386bc505a917799b8/include/boost/multiprecision/detail/default_ops.hpp#L1071  
> . In the case that `result` has a const char* construct/assign (ie 2 multiprecision classes) we could indeed remove lexical_cast here, otherwise I think we would just have to add a static_assert - "not implemented" message in the standalone use case?  
  
In math there are several places where `static_assert` messages show not implemented in the standalone case. These were used when it was overly painful to re-implement (e.g. lexical cast), or are language standard dependent (e.g. `std::gcd`)

---

## Comment 9

> Username: NAThompson  
> Created at: 2021-09-22 15:29:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-925040212  

> In math there are several places where static_assert messages show not implemented in the standalone case. These were used when it was overly painful to re-implement (e.g. lexical cast), or are language standard dependent (e.g. std::gcd)  
  
I'm fine with this. Obviously it's slightly inelegant but the alternative is users saying "I can't use boost.multiprecision because the dependency is too heavy. . ."

---

## Comment 10

> Username: mborland  
> Created at: 2022-03-01 08:44:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055165182  

@NAThompson now that https://github.com/boostorg/multiprecision/pull/418 is merged I think we are in a good position for an inaugural standalone release.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2022-03-01 09:34:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055215409  

>  in a good position for an inaugural standalone release  
  
Indeed. This is great.  
  
I did encounter something that confused me a bit. My nightly on develop branch yesterday hit a lot of errors on one of my custom multiprecision backends regarding the `precision` struct, which is in `boost::math::policies` namespace. Is there some guidance or something authors of custom number-backends need to be aware of when going standalone?

---

## Comment 12

> Username: mborland  
> Created at: 2022-03-01 09:47:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055227580  

@ckormanyos do you have an example? For the specializations from `boost::math` I added empty structs. Below is an example from `gmp.hpp`  
  
```  
namespace math { namespace tools {  
  
#ifndef BOOST_MP_MATH_AVAILABLE  
  
template <typename T>  
inline int digits();  
  
template <typename T>  
inline T max_value();  
  
template <typename T>  
inline T min_value();  
  
#endif // BOOST_MP_MATH_AVAILABLE  
```  
  
This way the code for `digits/max_value/min_value` is still valid.

---

## Comment 13

> Username: ckormanyos  
> Created at: 2022-03-01 10:01:30 UTC  
> Updated at: 2022-03-01 10:06:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055248311  

> do you have an example?  
  
Well, I had a regular nightly running on wide-integer, which pulls in Math, Multiprecision and Config from the develop branch.  
  
All of a sudden this morning I saw every single compiler/OS fail on my specialization of `boost::math::policies::precision`.  
  
The error I found was as shown below and can be seen in a link like [this](https://github.com/ckormanyos/wide-integer/runs/5369229696?check_suite_focus=true#step:5:30):  
  
```sh  
error: explicit specialization of undeclared template struct 'precision'  
```  
  
Edit: Correct `precision` spelling.

---

## Comment 14

> Username: ckormanyos  
> Created at: 2022-03-01 10:06:33 UTC  
> Updated at: 2022-03-01 10:07:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055254528  

For a while I thought, maybe I am not supposed to specialize `boost::math::policies::prrcision` any more when making a backend for Multiprecision. This would make sense because Math is now decoupled from Multiprecision via the `BOOST_MP_MATH_AVAILABLE` option.  
  
Maybe I was never supposed to specialize the `precision` struct in the first place? I had just gotten into the habit of doing so...?

---

## Comment 15

> Username: mborland  
> Created at: 2022-03-01 10:14:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055262874  

I think in your case you do something like the following:  
  
```  
#ifndef BOOST_MP_MATH_AVAILABLE  
  
template <typename... Args>  
struct precision {};  
  
template <typename T>  
struct digits2 {};  
  
#endif // BOOST_MP_MATH_AVAILABLE  
```  
  
You already have the specialization available for precision, but you will need to provide one as well for digits2.

---

## Comment 16

> Username: ckormanyos  
> Created at: 2022-03-01 10:44:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055290173  

> you will need to provide one as well for digits2  
  
Understood. Thanks. All working now.

---

## Comment 17

> Username: ckormanyos  
> Created at: 2022-03-01 10:46:44 UTC  
> Updated at: 2022-03-01 10:48:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1055292404  

Here is a trivial find that I made on MSVC.  
  
MSVC reports that the line [here](https://github.com/boostorg/multiprecision/blob/7e8d2aee205affe60a3aa165892b82b4d3b461cb/include/boost/multiprecision/cpp_bin_float.hpp#L380) has  
  
```sh  
unsafe use of type 'bool' in operation  
```  
  
I agree with the compiler. We could see if I find anything else. That's not much to fire off an entire CI run for, but lots of MSVC clients might stumble over it... Sorry i caught it late...  
  
Edit: It seems to be a Level 3 warning, so might get noticed...

---

## Comment 18

> Username: ckormanyos  
> Created at: 2022-03-24 07:46:58 UTC  
> Updated at: 2022-03-24 07:47:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1077331474  

Hi @NAThompson maybe we should close this issue?  
  
See also #403 and #418, bringing standalone into 1.79.  
  
Cc: @mborland and @jzmaddock

---

## Comment 19

> Username: NAThompson  
> Created at: 2022-03-24 15:41:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/364#issuecomment-1077768716  

Yeah, I think we can close.
