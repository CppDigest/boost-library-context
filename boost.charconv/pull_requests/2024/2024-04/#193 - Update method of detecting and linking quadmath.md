# #193 Update method of detecting and linking quadmath [Merged]

> Username: mborland  
> Created at: 2024-04-23 07:06:36 UTC  
> Updated at: 2025-10-29 15:35:41 UTC  
> Merged at: 2024-04-23 10:09:46 UTC  
> Closed at: 2024-04-23 10:09:46 UTC  
> Url: https://github.com/boostorg/charconv/pull/193  

Fixes: #191   
  
The next step in the never ending battle to get this correct. @pdimov Do you see any potential issues with this? I replaced `check_cxx_source_compiles` with `check_cxx_source_runs` due to Reuben's previous issue (https://github.com/boostorg/charconv/issues/176) where the header existed, but the compiled library did not.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-04-23 07:35:21 UTC  
> Updated_at: 2024-04-23 08:27:28 UTC  
> Url: https://github.com/boostorg/charconv/pull/193#issuecomment-2071626783  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/193?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.01%. Comparing base [(`007ac06`)](https://app.codecov.io/gh/boostorg/charconv/commit/007ac06561c59230bf3a5f0a8fd05079afff5ef2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f2d2aca`)](https://app.codecov.io/gh/boostorg/charconv/pull/193?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/193/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #193   +/-   ##  
========================================  
  Coverage    94.01%   94.01%             
========================================  
  Files           66       66             
  Lines         8439     8439             
========================================  
  Hits          7934     7934             
  Misses         505      505             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/193?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/193?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [007ac06...f2d2aca](https://app.codecov.io/gh/boostorg/charconv/pull/193?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-23 09:54:55 UTC  
> Url: https://github.com/boostorg/charconv/pull/193#issuecomment-2071895092  

This looks OK to me, by visual inspection alone.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-04-23 10:09:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/193#issuecomment-2071922400  

Looking at the old CI runs the `posix-cmake-test` suite all yielded `-- Boost.Charconv: quadmath support OFF`. Now the macOS runs show `-- Boost.Charconv: quadmath support OFF` as they should, and Ubuntu runs show `-- Boost.Charconv: quadmath support ON`.

---

## Comment 4

> Username: erikbs  
> Created_at: 2025-10-23 20:07:47 UTC  
> Url: https://github.com/boostorg/charconv/pull/193#issuecomment-3438932701  

@mborland This change breaks cross-compilation (`CMake Error: try_run() invoked in cross-compiling mode` ). I `grep`-ed the Boost source archive after `check_cxx_source_` and this seems to be the only place where `_runs` is used instead of `_compiles`. Following the pattern elsewhere I did this:  
```diff  
@@ -9,0 +10,2 @@ project(boost_charconv VERSION "${BOOST_SUPERPROJECT_VERSION}" LANGUAGES CXX)  
+include(CheckCXXSourceCompiles)  
+  
@@ -22,14 +23,0 @@ target_include_directories(boost_charconv PUBLIC include)  
-include(CheckCXXSourceRuns)  
-set(BOOST_CHARCONV_QUADMATH_TEST_SOURCE  
-"  
-#include <quadmath.h>  
-int main()  
-{  
-    __float128 f = -2.0Q;  
-    f = fabsq(f);  
-    __float128 big = HUGE_VALQ;  
-  
-    return f == big;  
-}  
-")  
-  
@@ -37 +25 @@ set(CMAKE_REQUIRED_LIBRARIES "quadmath")  
-check_cxx_source_runs("${BOOST_CHARCONV_QUADMATH_TEST_SOURCE}" BOOST_CHARCONV_QUADMATH_FOUND)  
+check_cxx_source_compiles("#include <${CMAKE_CURRENT_SOURCE_DIR}/config/has_float128.cpp>" BOOST_CHARCONV_QUADMATH_FOUND)  
```  
which seems to fix the problem for me (revert to `cxx_check_source_compiles`, but keep `CMAKE_REQUIRED_LIBRARIES` and set the source to including `has_float128.cpp`, because the original check had an incorrect source file containing nothing but that file path). From what I understand, `_compiles` is insufficient on Alpine because the header is present while the library is not? But wouldn't `set(CMAKE_REQUIRED_LIBRARIES "quadmath")` catch that case? I don't have an Alpine installation to test on, but I tested a minimal CMake example that has a `check_cxx_source_compiles`, then set `CMAKE_REQUIRED_LIBRARIES` to "does_not_exist" and it failed as expected.

---

## Comment 5

> Username: mborland  
> Created_at: 2025-10-29 13:39:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/193#issuecomment-3461582349  

@erikbs your patch has been merged in https://github.com/boostorg/charconv/pull/284. It was confirmed fine on the referenced Alpine Linux environment.

---

## Comment 6

> Username: erikbs  
> Created_at: 2025-10-29 15:35:41 UTC  
> Url: https://github.com/boostorg/charconv/pull/193#issuecomment-3462301840  

Great! Thanks!

---
