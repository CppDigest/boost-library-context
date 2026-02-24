# #1275 - Compilation errors on MSVC 14.0 [Closed]

> Username: Lastique  
> Created at: 2025-06-16 14:01:38 UTC  
> Updated at: 2025-06-27 15:39:27 UTC  
> Closed at: 2025-06-27 15:39:27 UTC  
> Url: https://github.com/boostorg/math/issues/1275  

Recently, Boost.Math was modified in a way that fails to compile with MSVC 14.0. This affects downstream libraries, specifically Boost.Parameter tests (which depend on Boost.Graph, which depends on Boost.Math):  
  
```  
MSVC 14.0 has broken C++14 constexpr support. Support for this compiler will be removed in Boost 1.86  
C:\projects\boost-root\boost/math/tools/promotion.hpp(90): error C2061: syntax error: identifier 'common_type_t<`template-type-parameter-1',`template-type-parameter-2',float>'  
C:\projects\boost-root\boost/math/tools/promotion.hpp(91): note: see reference to class template instantiation 'boost::math::tools::pa2_integral_already_removed<T1,T2,true>' being compiled  
C:\projects\boost-root\boost/math/tools/promotion.hpp(90): error C2238: unexpected token(s) preceding ';'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(325): error C2433: 'enable_if_t<,bool, ?? :: ?? >': 'inline' not permitted on data declarations  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(325): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(325): error C2061: syntax error: identifier 'iconvert'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(331): error C2065: 'T': undeclared identifier  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(331): error C3544: 'Args': parameter pack expects a type template argument  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(331): error C3245: 'boost::math::is_constructible_v': use of a variable template requires template argument list  
C:\projects\boost-root\boost/math/tools/type_traits.hpp(411): note: see declaration of 'boost::math::is_constructible_v'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(331): error C2975: '_Test': invalid template argument for 'std::enable_if_t', expected compile-time constant expression  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\type_traits(1875): note: see declaration of '_Test'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(332): error C2061: syntax error: identifier 'type'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(332): note: see reference to class template instantiation 'enable_if_t<false,int>' being compiled  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(332): error C2938: 'enable_if_t<false,int>' : Failed to specialize alias template  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(333): error C2143: syntax error: missing ';' before '{'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(333): error C2447: '{': missing function header (old-style formal list?)  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(340): error C2433: 'enable_if_t<,bool, ?? :: ?? >': 'inline' not permitted on data declarations  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(340): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(340): error C2061: syntax error: identifier 'lconvert'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(346): error C2065: 'T': undeclared identifier  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(346): error C3544: 'Args': parameter pack expects a type template argument  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(346): error C3245: 'boost::math::is_constructible_v': use of a variable template requires template argument list  
C:\projects\boost-root\boost/math/tools/type_traits.hpp(411): note: see declaration of 'boost::math::is_constructible_v'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(346): error C2975: '_Test': invalid template argument for 'std::enable_if_t', expected compile-time constant expression  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\type_traits(1875): note: see declaration of '_Test'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(347): error C2061: syntax error: identifier 'type'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(347): note: see reference to class template instantiation 'enable_if_t<false,long>' being compiled  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(347): error C2938: 'enable_if_t<false,long>' : Failed to specialize alias template  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(348): error C2143: syntax error: missing ';' before '{'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(348): error C2447: '{': missing function header (old-style formal list?)  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(355): error C2433: 'enable_if_t<,bool, ?? :: ?? >': 'inline' not permitted on data declarations  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(355): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(355): error C2061: syntax error: identifier 'llconvert'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(361): error C2065: 'T': undeclared identifier  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(361): error C3544: 'Args': parameter pack expects a type template argument  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(361): error C3245: 'boost::math::is_constructible_v': use of a variable template requires template argument list  
C:\projects\boost-root\boost/math/tools/type_traits.hpp(411): note: see declaration of 'boost::math::is_constructible_v'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(361): error C2975: '_Test': invalid template argument for 'std::enable_if_t', expected compile-time constant expression  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\type_traits(1875): note: see declaration of '_Test'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(362): error C2061: syntax error: identifier 'type'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(362): note: see reference to class template instantiation 'enable_if_t<false,__int64>' being compiled  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(362): error C2938: 'enable_if_t<false,__int64>' : Failed to specialize alias template  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(363): error C2143: syntax error: missing ';' before '{'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(363): error C2447: '{': missing function header (old-style formal list?)  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(370): error C2433: 'boost::math::enable_if_t<,bool, ?? :: ?? >': 'inline' not permitted on data declarations  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(370): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(370): error C2374: 'boost::math::enable_if_t<,bool, ?? :: ?? >': redefinition; multiple initialization  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(355): note: see declaration of 'boost::math::enable_if_t<,bool, ?? :: ?? >'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(370): error C2061: syntax error: identifier 'llconvertert'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(376): error C2065: 'T': undeclared identifier  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(376): error C3544: 'Args': parameter pack expects a type template argument  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(376): error C3245: 'boost::math::is_constructible_v': use of a variable template requires template argument list  
C:\projects\boost-root\boost/math/tools/type_traits.hpp(411): note: see declaration of 'boost::math::is_constructible_v'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(376): error C2975: '_Test': invalid template argument for 'std::enable_if_t', expected compile-time constant expression  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\type_traits(1875): note: see declaration of '_Test'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(377): error C2061: syntax error: identifier 'type'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(377): note: see reference to class template instantiation 'enable_if_t<false,__int64>' being compiled  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(377): error C2938: 'enable_if_t<false,__int64>' : Failed to specialize alias template  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(378): error C2143: syntax error: missing ';' before '{'  
C:\projects\boost-root\boost/math/special_functions/trunc.hpp(378): error C2447: '{': missing function header (old-style formal list?)  
C:\projects\boost-root\boost/math/special_functions/pow.hpp(126): error C2475: 'boost::math::pow': redefinition; 'constexpr' specifier mismatch  
C:\projects\boost-root\boost/math/special_functions/math_fwd.hpp(1173): note: see declaration of 'boost::math::pow'  
C:\projects\boost-root\boost/math/special_functions/pow.hpp(133): error C2475: 'boost::math::pow': redefinition; 'constexpr' specifier mismatch  
C:\projects\boost-root\boost/math/special_functions/math_fwd.hpp(1176): note: see declaration of 'boost::math::pow'  
    call "bin.v2\standalone\msvc\msvc-14.0\address-model-32\architecture-x86\msvc-setup.bat" x86 >nul  
 cl /Zm800 -nologo  -c -Fo"bin.v2\libs\math\build\msvc-14.0\debug\x86_32\cxxstd-14-iso\threading-multi\pch.obj" -Yc"../src/tr1/pch.hpp" -Yl"__bjam_pch_symbol_pch.hpp" -Fp"bin.v2\libs\math\build\msvc-14.0\debug\x86_32\cxxstd-14-iso\threading-multi\pch.pch" -TP /wd4675 /EHs /std:c++14 /GR /Zc:throwingNew /Z7 /Od /Ob0 /W0 /MDd /Zc:forScope /Zc:wchar_t /wd4996 /Zc:inline -DBOOST_ALL_NO_LIB=1 -DBOOST_BUILD_PCH_ENABLED -DBOOST_CONTAINER_DYN_LINK=1 -DBOOST_CONTAINER_NO_LIB=1 -DBOOST_MATH_TR1_DYN_LINK=1 -DBOOST_RANDOM_DYN_LINK=1 -DBOOST_RANDOM_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_LIB=1 "-I." "-IC:\projects\boost-root\libs\assert\include" "-IC:\projects\boost-root\libs\concept_check\include" "-IC:\projects\boost-root\libs\container\include" "-IC:\projects\boost-root\libs\container_hash\include" "-IC:\projects\boost-root\libs\core\include" "-IC:\projects\boost-root\libs\describe\include" "-IC:\projects\boost-root\libs\dynamic_bitset\include" "-IC:\projects\boost-root\libs\integer\include" "-IC:\projects\boost-root\libs\intrusive\include" "-IC:\projects\boost-root\libs\lexical_cast\include" "-IC:\projects\boost-root\libs\math\build" "-IC:\projects\boost-root\libs\math\include" "-IC:\projects\boost-root\libs\math\src\tr1" "-IC:\projects\boost-root\libs\move\include" "-IC:\projects\boost-root\libs\mp11\include" "-IC:\projects\boost-root\libs\predef\include" "-IC:\projects\boost-root\libs\preprocessor\include" "-IC:\projects\boost-root\libs\random\include" "-IC:\projects\boost-root\libs\static_assert\include" "-IC:\projects\boost-root\libs\system\include" "-IC:\projects\boost-root\libs\throw_exception\include" "-IC:\projects\boost-root\libs\type_traits\include" "-IC:\projects\boost-root\libs\utility\include" "-IC:\projects\boost-root\libs\variant2\include" "-IC:\projects\boost-root\libs\winapi\include" "-Ilibs\config\include" "bin.v2\libs\math\build\msvc-14.0\debug\x86_32\cxxstd-14-iso\threading-multi\pch.pch.cpp"   
```  
  
https://ci.appveyor.com/project/Lastique/parameter/builds/52243732/job/r4qrqpaklphyncrb?fullLog=true#L845  
  
I know the warning says MSVC 14.0 is no longer supported, but I think the `Jamfile` in Boost.Math should list the requirements that exclude the build on this compiler, which it currently doesn't. At least, `cxx14_constexpr` is missing (if the warning is to be believed), and some other requirement (currently non-existing in Boost.Config) that checks the presence of `*_t` and `*_v` versions of type traits in `<type_traits>`.
