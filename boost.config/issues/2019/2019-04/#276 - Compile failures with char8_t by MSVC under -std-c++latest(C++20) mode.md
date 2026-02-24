# #276 - Compile failures with char8_t by MSVC under /std:c++latest(C++20) mode [Closed]

> Username: QuellaZhang  
> Created at: 2019-04-08 08:01:19 UTC  
> Updated at: 2019-04-20 23:01:53 UTC  
> Closed at: 2019-04-16 18:37:46 UTC  
> Url: https://github.com/boostorg/config/issues/276  

We've stumbled across some build failures in Boost\libs\config\test after implementing support for char8_t under /std:c++latest in the development version of Visual C++. Could you help look at this? Thanks in advance! Noted that this issue only found when compiles with unreleased vctoolset, that next release of MSVC will have this behavior.   
  
**Repro steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3. set _CL_=/std:c++latest  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=86  
6. .\b2 variant=release --build-dir=..\out\x86rel address-model=86  
7. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\config\test  
  
**Failures:**  
config_test.i  
D:\Boost\src\libs\config\test\boost_no_unicode_literals.ipp(20): error C2440: 'initializing': cannot convert from 'const char8_t [1]' to 'const char *'  
D:\Boost\src\libs\config\test\boost_no_unicode_literals.ipp(20): note: Types pointed to are unrelated; conversion requires reinterpret_cast, C-style cast or function-style cast

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-04-12 17:22:06 UTC  
> Url: https://github.com/boostorg/config/issues/276#issuecomment-482655236  

OK, I assume this is a new C++20 feature - and one that breaks existing code given that `u8"xyz"` was previously of type `char[N]`?  Is there an SD6 macro for this change - I can't see anything on the "official" SD6 page yet which seems not to have caught up with C++20?  
  
Thanks, John.

---

## Comment 2

> Username: CaseyCarter  
> Created at: 2019-04-16 15:44:20 UTC  
> Url: https://github.com/boostorg/config/issues/276#issuecomment-483717726  

Hey John, Casey Carter here from MSVC.   
  
> Is there an SD6 macro for this change  
  
My understanding is that WG21 is no longer maintaining SD-6 now that the feature-test macros are being formally standardized and have moved into the working draft for C++20. There are two feature-test macros related to `char8_t`: [`__cpp_char8_t`](http://eel.is/c++draft/cpp.predefined#:__cpp_char8_t) indicates compiler support for `char8_t` as a type, and [`__cpp_lib_char8_t`](http://eel.is/c++draft/support.limits#lib:__cpp_lib_char8_t) indicates standard library support for e.g. `u8string`, `u8string_view`, `char_traits<char8_t>`, etc.  
  
And yes, `u8"foo"` has type `const char8_t[4]` in C++20 instead of `const char[4]` as in C++17. It's a breaking change, albeit one that WG21 thinks is for the best, but purity of motivation doesn't make it any less problematic to support in libraries that need to be portable between C++17 and C++20.  
  
Let me know if you have any questions or if there's anything I can do to ease the fix.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-04-16 18:38:21 UTC  
> Url: https://github.com/boostorg/config/issues/276#issuecomment-483794332  

Many thanks for the clarification - the commit referenced above should hopefully fix this now.

---

## Comment 4

> Username: CaseyCarter  
> Created at: 2019-04-20 23:01:53 UTC  
> Url: https://github.com/boostorg/config/issues/276#issuecomment-485191827  

> Many thanks for the clarification - the commit referenced above should hopefully fix this now.  
  
Looks right to me. Thanks for the quick response.
