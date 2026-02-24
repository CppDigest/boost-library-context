# #723 CCMATH Module [Closed]

> Username: mborland  
> Created at: 2021-11-28 12:09:15 UTC  
> Updated at: 2022-05-22 16:57:44 UTC  
> Closed at: 2022-05-22 16:57:44 UTC  
> Url: https://github.com/boostorg/math/pull/723  

@NAThompson This is a minimal working example of turning ccmath into a module. On GCC you can compile the test with:  
```  
gcc ccmath_module_test.cpp ../include/boost/math/ccmath/ccmath.cpp -std=c++20 -fmodules-ts  
```  
I would effectively copy all existing code into one giant file that would serve as the entire module. Let me know what you think.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-11-28 15:54:34 UTC  
> Url: https://github.com/boostorg/math/pull/723#issuecomment-981108287  

This is cool. Looks like most of the work will be to get the config into CI!

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-11-28 17:55:21 UTC  
> Url: https://github.com/boostorg/math/pull/723#issuecomment-981125729  

@mborland : Have you hit this one?  
  
```  
~/m/test ❯❯❯ g++ ccmath_module_test.cpp -I../include  ../include/boost/math/ccmath/ccmath.cpp -std=c++20 -fmodules-ts                      ✘ 1 ccmath_module ◼  
  
ccmath_module_test.cpp:6:8: fatal error: module 'boost_math_ccmath' not found  
import boost_math_ccmath;  
~~~~~~~^~~~~~~~~~~~~~~~~  
1 error generated.  
../include/boost/math/ccmath/ccmath.cpp:102:72: error: template parameter redefines default argument  
template <typename T, std::enable_if_t<!std::is_unsigned_v<T>, bool> = true>  
                                                                       ^  
../include/boost/math/ccmath/ccmath.cpp:32:72: note: previous default template argument defined here  
template <typename T, std::enable_if_t<!std::is_unsigned_v<T>, bool> = true>  
                                                                       ^  
../include/boost/math/ccmath/ccmath.cpp:118:71: error: template parameter redefines default argument  
template <typename T, std::enable_if_t<std::is_unsigned_v<T>, bool> = true>  
                                                                      ^  
../include/boost/math/ccmath/ccmath.cpp:35:71: note: previous default template argument defined here  
template <typename T, std::enable_if_t<std::is_unsigned_v<T>, bool> = true>  
                                                                      ^  
2 errors generated.  
```

---

## Comment 3

> Username: mborland  
> Created_at: 2021-11-29 06:37:11 UTC  
> Url: https://github.com/boostorg/math/pull/723#issuecomment-981331402  

> @mborland : Have you hit this one?  
  
I get that error if I compile with Clang 13.0.0, but not with GCC 11.2.1.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-12-30 15:45:31 UTC  
> Url: https://github.com/boostorg/math/pull/723#issuecomment-1003083119  

@mborland : Ok, looks like modules are a bit of a way off; I suggest we just keep the PR open and periodically try it out with new compilers . . .

---

## Comment 5

> Username: mborland  
> Created_at: 2022-05-22 16:57:44 UTC  
> Url: https://github.com/boostorg/math/pull/723#issuecomment-1133934573  

Superseded by #783

---
