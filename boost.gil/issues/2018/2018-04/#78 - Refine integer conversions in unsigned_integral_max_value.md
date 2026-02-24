# #78 - Refine integer conversions in unsigned_integral_max_value [Open]

> Username: mloskot  
> Created at: 2018-04-09 22:04:16 UTC  
> Updated at: 2019-07-08 22:12:13 UTC  
> Url: https://github.com/boostorg/gil/issues/78  

As per Andrey Semashev's suggestion https://lists.boost.org/Archives/boost/2018/04/242013.php,  
  
since we switched to C++11, it may be a good idea to  
- use the standard `constexpr std::numeric_limits<UnsignedIntegralChannel>::max()`  
- remove dependenc on `#include <boost/integer_traits.hpp>`  
  
Comments?

---

## Comment 1

> Username: T-Deuty  
> Created at: 2019-07-08 18:56:26 UTC  
> Updated at: 2019-07-08 18:58:37 UTC  
> Url: https://github.com/boostorg/gil/issues/78#issuecomment-509349197  

This seems to require the NOMINMAX preprocessor definition. I'd suggest adding that with the check for if its already defined before wherever windows.h is included, if in fact it is included.  
  
Perhaps it isn't in the GIL library and its just my large project, but I'm running into issues when I need both this to compile and code using the standard MAX macro.  
  
Edit:  
Adding this link to a separate issue where the same problem I'm trying to describe is brought up: https://github.com/boostorg/stacktrace/issues/76

---

## Comment 2

> Username: mloskot  
> Created at: 2019-07-08 20:21:03 UTC  
> Updated at: 2019-07-08 20:29:45 UTC  
> Url: https://github.com/boostorg/gil/issues/78#issuecomment-509376643  

@T-Deuty  
  
> This seems to require the NOMINMAX preprocessor definition  
  
Yes, Windows SDK/MSVC, in many, if not most uses of `std::numeric_limits<T>::max()` will either require to `#define NOMINMAX` or function-like macro suppression with extra parentheses `(std::numeric_limits<T>::max)()` or one of several other possible tricks to work around the Windows SDK/MSVC annoyance.  
  
> I'd suggest adding that with the check for if its already defined before wherever `windows.h` is included, if in fact it is included.  
  
AFAICT, nowhere in GIL the `windows.h` is included. However, to ensure building tests/examples is not prone to this annoyance, we `#define NOMINMAX` in the build configurations:  
  
https://github.com/boostorg/gil/blob/c3e84adbd730205ba601477492eb0fbc9ff2c230/test/Jamfile#L41  
https://github.com/boostorg/gil/blob/c3e84adbd730205ba601477492eb0fbc9ff2c230/CMakeLists.txt#L88  
  
IMO, it is not GIL or Boost or other C++ library duty but a library's user duty to `#define NOMINMAX` before any Windows header, as suggested by LLVM/clang/libcxx infrastructure:  
  
```  
#ifdef min  
#if !defined(_LIBCPP_DISABLE_MACRO_CONFLICT_WARNINGS)  
#if defined(_LIBCPP_WARNING)  
_LIBCPP_WARNING("macro min is incompatible with C++.  Try #define NOMINMAX "  
                "before any Windows header. #undefing min")  
#else  
#warning: macro min is incompatible with C++.  #undefing min  
#endif  
#endif  
#undef min  
#endif  
```  
  
Alternatively, we may consider adding very similar check and warning somewhere in `boost/gil.hpp`. What do you think, @stefanseefeld ?

---

## Comment 3

> Username: T-Deuty  
> Created at: 2019-07-08 21:56:53 UTC  
> Url: https://github.com/boostorg/gil/issues/78#issuecomment-509407218  

Thanks for getting back to me so quickly. I agree with your opinion that it is on the library user to handle this, I'll look into defining NOMINMAX for my project. I was doing so until I came across an error where we're using what I think is a standard definition of max, in another library, that won't compile with NOMINMAX defined. So feels like a catch 22 between using GIL and this other library.   
  
We're using Boost-gil 1.70 from Vcpkg by the way. My vote would be for the "function-like macro suppression with extra parenthesis" but in the meantime I'll try to find all the Windows headers and define NOMINMAX before including them.  
  
Thanks,  
Tyler

---

## Comment 4

> Username: mloskot  
> Created at: 2019-07-08 22:12:13 UTC  
> Url: https://github.com/boostorg/gil/issues/78#issuecomment-509411220  

> My vote would be for the "function-like macro suppression with extra parenthesis"   
  
I've searched through the code and apparently GIL already uses this technique in numerous places, e.g.  
  
https://github.com/boostorg/gil/blob/c3e84adbd730205ba601477492eb0fbc9ff2c230/include/boost/gil/color_convert.hpp#L147-L148  
  
as well as explicit template parameter to suppress the macro:  
  
https://github.com/boostorg/gil/blob/c3e84adbd730205ba601477492eb0fbc9ff2c230/include/boost/gil/algorithm.hpp#L975  
  
Please, consider [contributing a pull request](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md) with similar fixes that will help your project.  
Since such technique is already in use, I don't see any reason why we shouldn't accept it.
