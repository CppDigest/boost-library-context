# #677 - MSVC compilation error with /Zc:preprocessor (Enable preprocessor conformance mode) flag [Closed]

> Username: jtjbrady  
> Created at: 2025-04-17 12:07:23 UTC  
> Updated at: 2025-04-17 15:37:35 UTC  
> Closed at: 2025-04-17 14:41:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/677  

Using the example program from the README, just to be 100% clear that program being:  
```  
#include <boost/math/special_functions/gamma.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <sstream>  
  
auto main() -> int  
{  
    // Configure a multiprecision binary floating-point type with approximately  
    // 100 decimal digits of precision and expression templates enabled.  
    // Note that the popular type cpp_bin_float_100 has already been preconfigured  
    // and aliased in the multiprecision headers.  
  
    using big_float_type = boost::multiprecision::cpp_bin_float_100;  
  
    // In the next few lines, compute and compare sqrt(pi) with tgamma(1/2)  
    // using the 100-digit multiprecision type.  
  
    const big_float_type sqrt_pi { sqrt(boost::math::constants::pi<big_float_type>()) };  
  
    const big_float_type half { big_float_type(1) / 2 };  
  
    const big_float_type gamma_half { boost::math::tgamma(half) };  
  
    std::stringstream strm { };  
  
    // 1.772453850905516027298167483341145182797549456122387128213807789852911284591032181374950656738544665  
  
    strm << std::setprecision(std::numeric_limits<big_float_type>::digits10)  
         << "sqrt_pi   : "  
         << sqrt_pi  
         << "\ngamma_half: "  
         << gamma_half;  
  
    std::cout << strm.str() << std::endl;  
}  
```  
  
Compiled with boost 1.87.0, works fine, with or without /Zc:preprecessor option.  
Compiled with boost 1.88.0, works fine without /Zc:preprecessor option.  
  
Compiled with ```cl -c /Zc:preprocessor /EHsc -IC:\local\boost_1_88_0 main.cpp```  
  
Results in:  
  
```  
Microsoft (R) C/C++ Optimizing Compiler Version 19.43.34810 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
main.cpp  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): warning C5103: pasting '""_cppi' and '128' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2988: unrecognizable template declaration/definition  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2143: syntax error: missing ';' before 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2473: 'operator ""_cppi': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2365: 'boost::multiprecision::literals::operator ""_cppi': redefinition; previous definition was 'function'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(239): note: see declaration of 'boost::multiprecision::literals::operator ""_cppi'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2059: syntax error: 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2143: syntax error: missing ';' before '{'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2447: '{': missing function header (old-style formal list?)  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): warning C5103: pasting '""_cppui' and '128' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2473: 'operator ""_cppui': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2365: 'boost::multiprecision::literals::operator ""_cppui': redefinition; previous definition was 'function'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(246): note: see declaration of 'boost::multiprecision::literals::operator ""_cppui'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2065: 'STR': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C3546: '...': there are no parameter packs available to expand  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2975: 'l1': invalid template argument for 'boost::multiprecision::literals::detail::make_packed_value_from_str', expected compile-time constant expression  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(198): note: see declaration of 'l1'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2976: 'boost::multiprecision::literals::detail::make_packed_value_from_str': too few template arguments  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(199): note: see declaration of 'boost::multiprecision::literals::detail::make_packed_value_from_str'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2059: syntax error: 'return'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2065: 'pt': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): error C2923: 'boost::multiprecision::literals::detail::make_backend_from_pack': 'pt' is not a valid template type argument for parameter 'Pack'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(270): note: see declaration of 'pt'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): warning C5103: pasting '""_cppi' and '256' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2988: unrecognizable template declaration/definition  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2143: syntax error: missing ';' before 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2473: 'operator ""_cppi': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2059: syntax error: 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2143: syntax error: missing ';' before '{'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2447: '{': missing function header (old-style formal list?)  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): warning C5103: pasting '""_cppui' and '256' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2473: 'operator ""_cppui': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2065: 'STR': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C3546: '...': there are no parameter packs available to expand  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2975: 'l1': invalid template argument for 'boost::multiprecision::literals::detail::make_packed_value_from_str', expected compile-time constant expression  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(198): note: see declaration of 'l1'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2976: 'boost::multiprecision::literals::detail::make_packed_value_from_str': too few template arguments  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(199): note: see declaration of 'boost::multiprecision::literals::detail::make_packed_value_from_str'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2059: syntax error: 'return'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2065: 'pt': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): error C2923: 'boost::multiprecision::literals::detail::make_backend_from_pack': 'pt' is not a valid template type argument for parameter 'Pack'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(271): note: see declaration of 'pt'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): warning C5103: pasting '""_cppi' and '512' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2988: unrecognizable template declaration/definition  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2143: syntax error: missing ';' before 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2473: 'operator ""_cppi': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2059: syntax error: 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2143: syntax error: missing ';' before '{'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2447: '{': missing function header (old-style formal list?)  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): warning C5103: pasting '""_cppui' and '512' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2473: 'operator ""_cppui': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2065: 'STR': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C3546: '...': there are no parameter packs available to expand  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2975: 'l1': invalid template argument for 'boost::multiprecision::literals::detail::make_packed_value_from_str', expected compile-time constant expression  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(198): note: see declaration of 'l1'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2976: 'boost::multiprecision::literals::detail::make_packed_value_from_str': too few template arguments  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(199): note: see declaration of 'boost::multiprecision::literals::detail::make_packed_value_from_str'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2059: syntax error: 'return'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2065: 'pt': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): error C2923: 'boost::multiprecision::literals::detail::make_backend_from_pack': 'pt' is not a valid template type argument for parameter 'Pack'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(272): note: see declaration of 'pt'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): warning C5103: pasting '""_cppi' and '1024' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2988: unrecognizable template declaration/definition  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2143: syntax error: missing ';' before 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2473: 'operator ""_cppi': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2059: syntax error: 'constant'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2143: syntax error: missing ';' before '{'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2447: '{': missing function header (old-style formal list?)  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): warning C5103: pasting '""_cppui' and '1024' does not result in a valid preprocessing token  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(252): note: in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(33): note: in expansion of macro 'BOOST_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(34): note: in expansion of macro 'BOOST_DO_JOIN'  
C:\local\boost_1_88_0\boost/config/helper_macros.hpp(35): note: in expansion of macro 'BOOST_DO_JOIN2'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2473: 'operator ""_cppui': looks like a function definition, but there is no parameter list.  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2065: 'STR': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C3546: '...': there are no parameter packs available to expand  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2975: 'l1': invalid template argument for 'boost::multiprecision::literals::detail::make_packed_value_from_str', expected compile-time constant expression  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(198): note: see declaration of 'l1'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2976: 'boost::multiprecision::literals::detail::make_packed_value_from_str': too few template arguments  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(199): note: see declaration of 'boost::multiprecision::literals::detail::make_packed_value_from_str'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2059: syntax error: 'return'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2065: 'pt': undeclared identifier  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2923: 'boost::multiprecision::literals::detail::make_backend_from_pack': 'pt' is not a valid template type argument for parameter 'Pack'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): note: see declaration of 'pt'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(273): error C2059: syntax error: '}'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(275): error C2143: syntax error: missing ';' before '}'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(275): error C2988: unrecognizable template declaration/definition  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(275): error C2059: syntax error: '}'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(283): error C2143: syntax error: missing ';' before '{'  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(283): error C2447: '{': missing function header (old-style formal list?)  
C:\local\boost_1_88_0\boost/multiprecision/cpp_int/literals.hpp(283): fatal error C1003: error count exceeds 100; stopping compilation  
```

---

## Comment 1

> Username: mborland  
> Created at: 2025-04-17 12:51:45 UTC  
> Updated at: 2025-04-17 12:51:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/677#issuecomment-2812767750  

Can you see if the same problem exists if you run on the develop branch? I believe this was fixed by: https://github.com/boostorg/multiprecision/pull/667

---

## Comment 2

> Username: jtjbrady  
> Created at: 2025-04-17 14:41:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/677#issuecomment-2813177111  

Having run git bisect I can confirm that 2ed2ef7e4048b18592efc73131170b902e2806b2 is the first bad commit and that it was fixed by d9279817061b58b9201ef74980307fcb9a2089f6.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-04-17 15:37:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/677#issuecomment-2813357010  

Thanks for detecting, reporting and handling this @jtjbrady and @mborland. Sorry about the down time on the suffixes. Glad it works now.
