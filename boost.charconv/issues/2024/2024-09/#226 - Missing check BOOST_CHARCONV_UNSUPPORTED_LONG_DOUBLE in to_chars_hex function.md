# #226 - Missing check BOOST_CHARCONV_UNSUPPORTED_LONG_DOUBLE in to_chars_hex function [Closed]

> Username: lucabonnix  
> Created at: 2024-09-16 09:31:54 UTC  
> Updated at: 2024-09-17 07:29:18 UTC  
> Closed at: 2024-09-16 12:01:44 UTC  
> Url: https://github.com/boostorg/charconv/issues/226  

https://github.com/boostorg/charconv/blob/bde521e1c9db72f8898005f17950dad4dd830a90/src/to_chars_float_impl.hpp#L276  
  
If the platform has an unsupported long double representation (e.g. ppc64le has 106 bits mantissa and 1024 max exponent), compile error will raise due to unknown ieee754_binary80 function.  
  
Patch to solve the issue:  
```  
--- boost_1_86_0/libs/charconv/src/to_chars_float_impl.hpp.orig 2024-08-08 00:07:34.000000000 +0200  
+++ boost_1_86_0/libs/charconv/src/to_chars_float_impl.hpp      2024-09-16 10:33:24.534310522 +0200  
@@ -273,7 +273,7 @@  
   
         typename std::conditional<std::is_same<Real, float>::value, ieee754_binary32,  
             typename std::conditional<std::is_same<Real, double>::value, ieee754_binary64,  
-                    #ifdef BOOST_CHARCONV_HAS_FLOAT128  
+                    #if defined(BOOST_CHARCONV_HAS_FLOAT128) && !defined(BOOST_CHARCONV_UNSUPPORTED_LONG_DOUBLE)  
                     typename std::conditional<std::is_same<Real, __float128>::value || BOOST_CHARCONV_LDBL_BITS == 128, ieee754_binary128, ieee754_binary80>::type  
                     #elif BOOST_CHARCONV_LDBL_BITS == 128  
                     ieee754_binary128  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-09-16 12:01:44 UTC  
> Url: https://github.com/boostorg/charconv/issues/226#issuecomment-2352720993  

I believe this is a duplicate of: https://github.com/boostorg/charconv/issues/220 which was fixed by: https://github.com/boostorg/charconv/pull/222.

---

## Comment 2

> Username: lucabonnix  
> Created at: 2024-09-17 07:29:16 UTC  
> Url: https://github.com/boostorg/charconv/issues/226#issuecomment-2354755153  

Tried the patch in #222 : it works perfectly! Many thanks!
