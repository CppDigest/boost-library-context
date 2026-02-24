# #76 - Marco collision let compilation errors on Windows [Closed]

> Username: HenryAWE  
> Created at: 2019-05-03 05:36:10 UTC  
> Updated at: 2019-07-08 20:28:38 UTC  
> Closed at: 2019-05-04 17:33:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/76  

The library on Windows brings the min/max macro from some Windows SDK headers into the global namespace that will cause some library using these functions (like Boost.GIL) fail to compile.  
  
My platform:Windows 10 64-bit  
Compiler: VS2017  
Boost version:1.69.0

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-05-03 18:28:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/76#issuecomment-489194828  

That's strange. There is a test that makes sure that windows.h does not leak into https://github.com/boostorg/stacktrace/blob/5c6740b68067cbd7070d2965bfbce32e81f680c9/test/test_impl.hpp#L15  
  
What build flags were you using? Probably `BOOST_USE_WINDOWS_H` was defined?

---

## Comment 2

> Username: HenryAWE  
> Created at: 2019-05-04 02:43:21 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/76#issuecomment-489287294  

I checked my project settings.`BOOST_USE_WINDOWS_H` wasn't defined.  
I added a test.cpp into my project with following code  
`#include <boost/stacktrace.hpp>`  
`#include <boost/gil.hpp>`  
It cannot reproduce the error.  
But when I include the stacktrace library header before other headers in a .cpp file in my project that **perviously** works well,this error appeared again.  
It seems that some three-party libraries and Boost.Stacktrace cause this problem together.  
Does Boost.Stacktrace defined some macros that will affect other libraries?  
  
Included headers of other libraries in that .cpp file  
- Boost 1.69.0 (Python , GIL , ProgramOptions , Containers)  
- SDL2 (I'm sure that l've not include SDL_syswm.h,which will include Windows.h)  
- SDL mixer  
- glew  
- glm  
  
Defined macros: `ZLIB_WINAPI` and marcos defined by MSVC in x64 release mode

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-05-04 17:33:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/76#issuecomment-489347839  

There are two solutions to your problem:  
* do not use a header only version of the Boost.Stacktrace library. Link with the WinDBG version and all your macro sufferings should be gone: https://www.boost.org/doc/libs/1_69_0/doc/html/stacktrace/configuration_and_build.html  
* or define `NOMINMAX` for your project  
  
Also please report the min/max issue to the GIL library. All the Boost libraries should workaround the min/max issue https://www.boost.org/development/requirements.html  
  
I'm closing this issue, as there are workarounds and it is almost impossible to remove the `windows.h` inclusion from this file (PR from brave ones are welcomed!):  
https://github.com/boostorg/stacktrace/blob/1ad62e582aecb656faaf0c30fcf42fca4656cfbc/include/boost/stacktrace/detail/frame_msvc.ipp#L21

---

## Comment 4

> Username: mloskot  
> Created at: 2019-07-08 20:28:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/76#issuecomment-509379250  

Copied from https://github.com/boostorg/gil/issues/78#issuecomment-509376643  
  
IMO,  it is not GIL or Boost or other C++ library duty but a library's user duty to `#define NOMINMAX` before any Windows header, as it is [suggested by LLVM/clang/libcxx infrastructure](https://github.com/llvm-mirror/libcxx/blob/4abbf7dac321f2bfa91e922c9033c86d9137b077/include/__undef_macros#L11-L33):  
  
```cpp  
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
