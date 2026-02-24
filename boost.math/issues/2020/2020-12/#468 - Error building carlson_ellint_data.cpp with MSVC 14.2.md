# #468 - Error building carlson_ellint_data.cpp with MSVC 14.2 [Closed]

> Username: evanmiller  
> Created at: 2020-12-23 02:50:18 UTC  
> Updated at: 2021-01-11 17:38:27 UTC  
> Closed at: 2021-01-11 17:38:27 UTC  
> Url: https://github.com/boostorg/math/issues/468  

Not sure if this is a problem with math or with random, but here's the log:  
  
https://github.com/evanmiller/math/runs/1598090904  
  
Relevant portion (starts at line 166 above):  
  
```  
compile-c-c++ ..\..\..\bin.v2\libs\math\tools\93289947ab6401ecb6a7cac258adb7b6\carlson_ellint_data.obj  
carlson_ellint_data.cpp  
D:\a\math\boost-root\boost/random/mixmax.hpp(65): warning C4003: not enough arguments for function-like macro invocation 'min'  
D:\a\math\boost-root\boost/random/mixmax.hpp(66): warning C4003: not enough arguments for function-like macro invocation 'max'  
D:\a\math\boost-root\boost/random/mixmax.hpp(65): error C2059: syntax error: ')'  
D:\a\math\boost-root\boost/random/mixmax.hpp(174): note: see reference to class template instantiation 'boost::random::mixmax_engine<Ndim,SPECIALMUL,SPECIAL>' being compiled  
D:\a\math\boost-root\boost/random/mixmax.hpp(65): error C2334: unexpected token(s) preceding ':'; skipping apparent function body  
D:\a\math\boost-root\boost/random/mixmax.hpp(174): error C2143: syntax error: missing ')' before ';'  
D:\a\math\boost-root\boost/random/mixmax.hpp(174): error C2059: syntax error: ')'  
D:\a\math\boost-root\boost/random/mixmax.hpp(174): error C2238: unexpected token(s) preceding ';'  
D:\a\math\boost-root\boost/random/mixmax.hpp(174): fatal error C1201: unable to continue after syntax error in class template definition  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-23 11:59:53 UTC  
> Url: https://github.com/boostorg/math/issues/468#issuecomment-750223416  

Boost.Random, see linked issue https://github.com/boostorg/random/issues/74.

---

## Comment 2

> Username: kotika  
> Created at: 2020-12-26 20:12:56 UTC  
> Updated at: 2020-12-26 20:13:34 UTC  
> Url: https://github.com/boostorg/math/issues/468#issuecomment-751391858  

I have fixed mixmax.hpp and created a PR, the patch is courtesy of jzmaddock:  
```  
--- mixmax.hpp.old	2020-12-26 17:52:17.000000000 +0200  
+++ mixmax.hpp	2020-12-26 17:53:53.000000000 +0200  
@@ -61,8 +61,8 @@  
     typedef std::uint64_t result_type ;  
     BOOST_STATIC_CONSTANT(std::uint64_t,mixmax_min=0);  
     BOOST_STATIC_CONSTANT(std::uint64_t,mixmax_max=((1ULL<<61)-1));  
-    BOOST_STATIC_CONSTEXPR result_type min() {return mixmax_min;}  
-    BOOST_STATIC_CONSTEXPR result_type max() {return mixmax_max;}  
+    BOOST_STATIC_CONSTEXPR result_type min BOOST_PREVENT_MACRO_SUBSTITUTION () {return mixmax_min;}  
+    BOOST_STATIC_CONSTEXPR result_type max BOOST_PREVENT_MACRO_SUBSTITUTION () {return mixmax_max;}  
     static const bool has_fixed_range = false;  
     BOOST_STATIC_CONSTANT(int,N=Ndim);     ///< The main internal parameter, size of the defining MIXMAX matrix  
     // CONSTRUCTORS:  
  
```
