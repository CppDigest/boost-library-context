# #503 - Build failures on Opensuse Tumbleweed caused by FLOAT128 detection in gcc [Open]

> Username: jcelerier  
> Created at: 2024-09-01 13:51:20 UTC  
> Updated at: 2024-09-01 13:51:20 UTC  
> Url: https://github.com/boostorg/config/issues/503  

GCC 14 does not seem to ship quadmath.h by default.   
If a library wants to do FLOAT128 (for instance: boost multiprecision) they must check for __has_include(<quadmath.h>) - checking for BOOST_HAS_FLOAT128 ought to be enough:   
  
https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_bin_float.hpp#L39-L44  
  
This causes for instance build errors for boost <= 1.85 there.   
  
To be more robust, this code here:  
https://github.com/boostorg/config/blob/develop/include/boost/config/compiler/gcc.hpp#L164-L172  
  
should have the same check for <quadmath.h> that clang has:   
https://github.com/boostorg/config/blob/develop/include/boost/config/compiler/clang.hpp#L60
