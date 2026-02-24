# #122 - Xoshiro Wundef [Closed]

> Username: mborland  
> Created at: 2025-01-31 14:46:50 UTC  
> Updated at: 2025-01-31 17:21:57 UTC  
> Closed at: 2025-01-31 17:21:57 UTC  
> Url: https://github.com/boostorg/random/issues/122  

```  
In file included from ./boost/random.hpp:52:0,  
                 from libs/decimal/test/test_boost_math_univariate_stats.cpp:46:  
./boost/random/xoshiro.hpp:98:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:107:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:116:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:304:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:313:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:322:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:458:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:467:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
./boost/random/xoshiro.hpp:476:40: error: "_MSVC_LANG" is not defined, evaluates to 0 [-Werror=undef]  
         #if (__cplusplus >= 201703L || _MSVC_LANG >= 201703L) && defined(__cpp_hex_float) && __cpp_hex_float >= 201603L  
                                        ^~~~~~~~~~  
cc1plus: all warnings being treated as errors  
```
