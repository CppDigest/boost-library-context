# #678 Fix standalone endianness macros [Merged]

> Username: StephanTLavavej  
> Created at: 2021-08-24 01:13:52 UTC  
> Updated at: 2021-08-24 19:26:12 UTC  
> Merged at: 2021-08-24 14:36:44 UTC  
> Closed at: 2021-08-24 14:36:44 UTC  
> Url: https://github.com/boostorg/math/pull/678  

* Fix comment typos: "endinaness" to "endianness"  
* Change the C++20 checks (for both `__cplusplus` and `_MSVC_LANG`) to be strict: `> 202000L` to `>= 202002L`  
* Add an `#error` when C++20 `<bit>` is missing, just in case.  
* Fix the `_WIN32` definitions. All Windows platforms are little-endian (regardless of x86/x64/ARM/ARM64), `<boost/predef/other/endian.h>` correctly reports little-endian, and MSVC's STL simply hardcodes `enum class endian { little = 0, big = 1, native = little };`, see https://github.com/microsoft/STL/blob/f75c7f596c7b491168fefb5eff5164ab7ae36867/stl/inc/bit#L271 .  
* Add a `static_assert` to verify that exactly one of `BOOST_MATH_ENDIAN_BIG_BYTE` or `BOOST_MATH_ENDIAN_LITTLE_BYTE` is true. This avoids the need for "Endian type could not be identified" consistency checks below.  
* Fix boostorg/math#677 by not testing `BOOST_MATH_ENDIAN_BIG_BYTE` and `BOOST_MATH_ENDIAN_LITTLE_BYTE` with the preprocessor, as `std::endian::native` isn't a preprocessor constant. Now, this simply expects `BOOST_MATH_ENDIAN_BIG_BYTE` to be usable in a constant expression, and assumes (due to the consistency check above) that if we aren't big-endian, we must be little-endian.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-08-24 02:43:23 UTC  
> Url: https://github.com/boostorg/math/pull/678#issuecomment-904277319  

Respect for your PR description game. Will merge once the CI cycles and deploy another standalone.

---

## Comment 2

> Username: AlexGuteniev  
> Created_at: 2021-08-24 08:24:37 UTC  
> Updated_at: 2021-08-24 09:17:21 UTC  
> Url: https://github.com/boostorg/math/pull/678#issuecomment-904431346  

~I'm wondering why did it fail on specifically ARM64?~  
  
~The check is here:  
https://github.com/boostorg/math/blob/564967785f2d01f755b1a38bb1f71c3484992d87/include/boost/math/special_functions/detail/fp_traits.hpp#L221-L222~  
  
~MSVC is not C++Builder, it supports in-class member initialization, and there should be `int64_t` on every platform MSVC supports. One of the constants is a lie. The endianness-aware code shouldn't have been ever used at all.~  
  
~Edit: for me it picks correct (the other) compilation branch~  
  
Nevermind, recreated. It is for `long double, extended_double_precision`

---
