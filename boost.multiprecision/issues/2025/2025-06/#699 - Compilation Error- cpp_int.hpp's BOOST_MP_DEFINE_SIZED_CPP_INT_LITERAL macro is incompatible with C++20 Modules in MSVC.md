# #699 - Compilation Error: cpp_int.hpp's BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL macro is incompatible with C++20 Modules in MSVC [Closed]

> Username: LBV2012-26  
> Created at: 2025-06-24 07:19:57 UTC  
> Updated at: 2025-07-01 01:15:02 UTC  
> Closed at: 2025-06-26 17:28:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/699  

I am experiencing a compilation failure when trying to use boost/multiprecision/cpp_int.hpp within a C++20 module interface file (.ixx) with the Microsoft Visual C++ (MSVC) compiler.  
  
The issue prevents types like uint128_t from being used in the public API of a module (e.g., as a function return value).  
  
- Compiler: MSVC v19.44.35211.0  
- Visual Studio Version: v17.14.7 Preview 1.0  
- Boost Version: 1.88.0  
- Operating System: Windows 11 24H2  
- C++ Standard: /std:c++latest  
- Build Configuration: Using C++20 Modules (.ixx files)  
  
**Problem Description:**  
When boost/multiprecision/cpp_int.hpp is included in the global module fragment of a module interface file, the compilation fails with a series of errors originating from boost/multiprecision/cpp_int/literals.hpp.  
The same failure occurs when attempting to use C++20 Header Units via import <boost/multiprecision/cpp_int.hpp>;. This indicates that the header itself is not currently compilable as a header unit by MSVC.  
  
**Minimal Reproducible Example:**  
Create a single module interface file MyModule.ixx with the following content:  
```  
// MyModule.ixx  
module;  
  
// Including the header here fails during compilation.  
#include <boost/multiprecision/cpp_int.hpp>  
  
export module MyModule;  
  
// The goal is to use uint128_t in the module's public interface.  
export boost::multiprecision::uint128_t get_value()  
{  
    return 1;  
}  
```  
  
The compilation produces the following errors:  
  
```  
<path>\include\boost\multiprecision\cpp_int\literals.hpp(270,1): warning C5103: pasting '""_cppi"' and '128' does not result in a valid preprocessing token  
<path>\include\boost\multiprecision\cpp_int\literals.hpp(252,9): message : in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
<path>\include\boost\config\helper_macros.hpp(33,9): message : in expansion of macro 'BOOST_JOIN'  
...  
<path>\include\boost\multiprecision\cpp_int\literals.hpp(270,1): error C2988: unrecognizable template declaration/definition  
<path>\include\boost\multiprecision\cpp_int\literals.hpp(270,1): error C2143: syntax error: missing ';' before 'constant'  
<path>\include\boost\multiprecision\cpp_int\literals.hpp(270,1): error C2473: 'operator ""_cppi': looks like a function definition, but there is no parameter list.  
<path>\include\boost\multiprecision\cpp_int\literals.hpp(270,1): error C2365: 'boost::multiprecision::literals::operator ""_cppi': redefinition; previous definition was 'function'  
...  
(another lots of errors)  
  
```  
I think this issue may caused by those following reasons:  
The core of the issue appears to be the BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL macro in literals.hpp.  
This macro uses BOOST_JOIN (which resolves to the preprocessor ## operator) to create user-defined literals like operator ""_cppi128. It attempts to paste the tokens ""_cppi and 128.  
The MSVC preprocessor, when compiling in the stricter C++20 modules mode, correctly identifies that ""_cppi is not a valid single token for pasting. This results in warning C5103 and a cascade of fatal syntax errors. This behavior seems to be more standard-compliant than in the traditional compilation mode, where it might have been tolerated.  
  
**Workarounds Attempted (and failed):**  
- Using Header Units (import <...>): Fails with the same error, as the header unit compilation itself fails.  
- PIMPL Idiom: This can hide the type from the interface, but it's not a solution when the type (uint128_t) must be part of the public API (e.g., as a return value or function parameter).  
  
This compatibility issue seems to require a change in how these user-defined literals are generated in the library to make them compatible with modern C++20 module compilation.  
  
I tried those methods to resolve, and it can compile successfully:  
```  
// original code:  
#define BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL(Bits)\  
   template <char... STR>\  
   constexpr boost:: ... operator BOOST_JOIN("" _cppi, Bits)() \  
   ...  
   template <char... STR>\  
   constexpr boost:: ... operator BOOST_JOIN("" _cppui, Bits)() \  
   ...  
  
// modified code:  
#define BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL(Bits)\  
   template <char... STR>\  
   constexpr boost:: ... operator "" BOOST_JOIN(_cppi, Bits)() \  
   ...  
   template <char... STR>\  
   constexpr boost:: ... operator "" BOOST_JOIN(_cppui, Bits)() \  
   ...  
```  
  
Thank you.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-06-26 17:28:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/699#issuecomment-3009225177  

I believe this was fixed in https://github.com/boostorg/multiprecision/pull/667, can you try latest develop?  If it's not already fixed please do reopen.

---

## Comment 2

> Username: LBV2012-26  
> Created at: 2025-07-01 01:12:31 UTC  
> Updated at: 2025-07-01 01:15:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/699#issuecomment-3021343980  

> I believe this was fixed in [#667](https://github.com/boostorg/multiprecision/pull/667), can you try latest develop? If it's not already fixed please do reopen.  
  
Of course, I will try it later. Thank you
