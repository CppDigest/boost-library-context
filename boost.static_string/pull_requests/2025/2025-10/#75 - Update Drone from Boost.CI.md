# #75 Update Drone from Boost.CI [Merged]

> Username: Flamefire  
> Created at: 2025-10-23 13:53:35 UTC  
> Updated at: 2025-10-24 11:46:32 UTC  
> Merged at: 2025-10-24 09:57:55 UTC  
> Closed at: 2025-10-24 09:57:55 UTC  
> Url: https://github.com/boostorg/static_string/pull/75  

This adds a couple new jobs and replaces common code by that from Boost.CI

---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2025-10-23 14:08:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/75#pullrequestreview-3370158354  

📁 .drone.star

```diff
   7 |- 
   5 |+ # Copyright (c) 2020 Rene Rivera
   6 |+ # Copyright (c) 2022-2025 Alexander Grund
```

> Username: Flamefire  
> Created_at: 2025-10-23 14:08:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/75#discussion_r2455280995  

```diff  
-# Copyright (c) 2022-2025 Alexander Grund  
+# Copyright (c) 2022 Alan de Freitas  
+# Copyright (c) 2022-2025 Alexander Grund  
```


---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-23 15:49:27 UTC  
> Url: https://github.com/boostorg/static_string/pull/75#issuecomment-3437761722  

@gennaroprota The C++20/23 builds are failing:  
```  
clang-linux.compile.c++ bin.v2/libs/static_string/test/static_string.test/clang-linux-15/release/x86_64/cxxstd-20-iso/threading-multi/visibility-hidden/static_string.o  
In file included from libs/static_string/test/static_string.cpp:13:  
libs/static_string/test/constexpr_tests.hpp:175:5: error: no member named 'subview' in 'boost::static_strings::basic_static_string<50, char, boost::static_strings::cxper_char_traits>'  
  a.subview(0);  
  ~ ^  
libs/static_string/test/static_string.cpp:7575:18: error: constexpr variable 'cxper' must be initialized by a constant expression  
  constexpr auto cxper = testConstantEvaluation();  
                 ^       ~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
This seems to be a real bug as the C++17 build seems to work. Any ideas?

---

## Comment 3

> Username: gennaroprota  
> Created_at: 2025-10-23 16:07:54 UTC  
> Url: https://github.com/boostorg/static_string/pull/75#issuecomment-3437859985  

`subview()` is only defined if `BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW` is defined, and config.hpp has the following code:  
  
```  
#if !defined(BOOST_STATIC_STRING_STANDALONE) || \  
     defined(BOOST_STATIC_STRING_HAS_STD_STRING_VIEW)  
#define BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW  
```

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-10-23 16:51:05 UTC  
> Url: https://github.com/boostorg/static_string/pull/75#issuecomment-3438074665  

> `subview()` is only defined if `BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW` is defined, and config.hpp has the following code:  
>   
> ```  
> #if !defined(BOOST_STATIC_STRING_STANDALONE) || \  
>      defined(BOOST_STATIC_STRING_HAS_STD_STRING_VIEW)  
> #define BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW  
> ```  
  
There is a bug with the checks. Fixed in https://github.com/boostorg/static_string/pull/76

---
