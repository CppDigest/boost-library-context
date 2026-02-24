# #196 Rewrite modules following the new recommended Boost practice [Merged]

> Username: apolukhin  
> Created at: 2025-04-07 14:10:53 UTC  
> Updated at: 2025-04-16 06:16:12 UTC  
> Merged at: 2025-04-16 06:16:09 UTC  
> Closed at: 2025-04-16 06:16:09 UTC  
> Url: https://github.com/boostorg/pfr/pull/196  

Changes:  
  
1) `#include <boost/pfr...` is now implicitly changed into `import boost.pfr` if the modules are supported   
2) CI now tests modules on Ubuntu 24.04  
3) Renamed module to `boost.pfr`  
4) CMakeLists.txt now uses modules for `Boost::pfr` target if modules are supported  
5) All the library internals now have unconditional module level linkage. `1)` allows users to mix `#include <boost/pfr...` and `import boost.pfr` in user code without ODR-violations.  
  
Significant differences from https://anarthal.github.io/cppblog/modules3:  
* PFR uses a separate macro for `import std;` / `includes`. This allows using `boost.pfr` module in C++20 and even without usable  `std` module.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-04-07 17:54:23 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#issuecomment-2784132203  

@anarthal I'd appreciate comments on this PR on modules (as you proposed at https://github.com/boostorg/core/pull/191#issuecomment-2783677013)

---

## Comment 2

> Username: coveralls  
> Created_at: 2025-04-07 20:14:38 UTC  
> Updated_at: 2025-04-14 13:49:23 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#issuecomment-2784519517  

## Pull Request Test Coverage Report for [Build 14447165733](https://coveralls.io/builds/73214464)  
  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/73214464/badge)](https://coveralls.io/builds/73214464) |  
| :-- | --: |  
| Change from base [Build 12675824451](https://coveralls.io/builds/71641678): |  0.0% |  
| Covered Lines: | 407 |  
| Relevant Lines: | 407 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-04-08 10:55:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#issuecomment-2786041355  

> Note that 2) seems to make it safe to change all the library internals to have module level linkage and still allows users to mix #include <boost/pfr... and import boost.pfr in user code without ODR-violations. I'll try to implement it next time  
  
Have you tried MSVC? My experience is that clang allows for almost anything, with MSVC being much more restrictive.

---

## Review 4 [Commented]

> Username: anarthal  
> Created_at: 2025-04-08 10:56:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/196#pullrequestreview-2749554387  

📁 .github/workflows/ci.yml

```diff
  92 |           ./b2 variant=debug tools/inspect/build
  93 | 
  94 |+       - name: Run modules tests
```

> Username: anarthal  
> Created_at: 2025-04-08 10:56:50 UTC  
> Updated_at: 2025-04-08 10:56:51 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2032936472  

I'd strongly advise to add a job covering modules under MSVC, since it's much more restrictive than clang. You can have a look at my mp11/charconv proposal for some inspiration.


---

## Review 5 [Commented]

> Username: anarthal  
> Created_at: 2025-04-08 14:29:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/196#pullrequestreview-2749589861  

What problems did you encounter in the tests that you commented out? I might be able to help with them.

📁 include/boost/pfr/core.hpp

```diff
  24 |+ #include <boost/pfr/tuple_size.hpp>
  25 |+ 
  26 |+ #if defined(BOOST_USE_STD_MODULE)
```

> Username: anarthal  
> Created_at: 2025-04-08 11:10:27 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2032957850  

Since PFR is the only library supporting this atm, I'd consider renaming the macro to BOOST_PFR_USE_STD_MODULE

---

📁 include/boost/pfr/core.hpp

```diff
  26 |+ #if defined(BOOST_USE_STD_MODULE)
  27 |+ import std;
  28 |+ #else
```

> Username: anarthal  
> Created_at: 2025-04-08 11:13:00 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2032961592  

I'd consider replacing this ifdef + includes by either a file like this:  
  
https://github.com/anarthal/mp11/blob/feature/cxx20-modules/include/boost/mp11/detail/std/tuple.hpp  
  
or follow this approach: https://github.com/boostorg/charconv/pull/255#issuecomment-2784613210

> Username: apolukhin  
> Created_at: 2025-04-10 14:38:57 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2037583058  

I'd rather leave it as is for now. Maybe I'll change it in the next PR


📁 .github/workflows/ci.yml

```diff
  98 |+           mkdir build_module
  99 |+           cd build_module
 100 |+           cmake -DBOOST_USE_MODULES=1 -DBUILD_TESTING=1 -GNinja -DCMAKE_CXX_COMPILER=clang++-19 -DCMAKE_CXX_FLAGS=-I../../../ ..
```

> Username: anarthal  
> Created_at: 2025-04-08 11:27:31 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2032985394  

If you're going to support building with and without import std, you need to add a CI build for each config/compiler pair. I think you're missing an include in the import std configuration.


📁 include/boost/pfr/detail/fields_count.hpp

```diff
  14 | 
  15 |- #ifdef BOOST_PFR_HAS_STD_MODULE
  15 |+ #if defined(BOOST_USE_STD_MODULE)
```

> Username: anarthal  
> Created_at: 2025-04-08 11:29:34 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2032988806  

I don't think you will get macro definitions like `__cpp_lib_is_aggregate` without an include (like `<version>`). `#include <version>` is safe to use in general, and can be mixed with imports without problem, but you will need to guard it (it's C++20 and above).  
  
Same applies for `SIZE_MAX`. You can replace this by `(std::numeric_limits<std::size_t>::max)()` and avoid the problem.

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
  16 | import std;
  17 | #else
  18 | #include <climits>      // CHAR_BIT
```

> Username: anarthal  
> Created_at: 2025-04-08 11:32:44 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2032994271  

You won't get `CHAR_BIT` with this structure. You will need to include the header even in modular builds. IIRC `#include <climits>` caused problems under MSVC when mixed with imports, but `#include <limits.h>` is okay.

> Username: apolukhin  
> Created_at: 2025-04-10 14:07:49 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2037516148  

I'll just use `std::numeric_limits<unsigned char>::digits` instead


📁 include/boost/pfr/detail/functional.hpp

```diff
  12 |+ #include <boost/pfr/detail/sequence_tuple.hpp>
  13 |+ 
  14 |+ #if defined(BOOST_USE_STD_MODULE)
```

> Username: anarthal  
> Created_at: 2025-04-08 11:35:36 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2032998500  

Same with `INT64_MIN` and friends here.


📁 include/boost/pfr/detail/possible_reflectable.hpp

```diff
  12 | 
  13 |- #ifdef BOOST_PFR_HAS_STD_MODULE
  13 |+ #if defined(BOOST_USE_STD_MODULE)
```

> Username: anarthal  
> Created_at: 2025-04-08 11:37:05 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2033000964  

`__cpp_lib_is_aggregate`


📁 include/boost/pfr/config.hpp

```diff
  71 | 
  72 | #ifndef BOOST_PFR_USE_STD_MAKE_INTEGRAL_SEQUENCE
  73 |+ #   if defined(BOOST_USE_MODULES)
```

> Username: anarthal  
> Created_at: 2025-04-08 11:39:10 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2033004192  

Maybe you can attempt to include `<version>` to get `__GLIBCXX__`? Although that makes the include heavier.


📁 CMakeLists.txt

```diff
  16 |+             ${CMAKE_CURRENT_LIST_DIR}/modules/pfr.cppm
  17 |+     )
  18 |+     if (CXX_MODULE_STD)
```

> Username: anarthal  
> Created_at: 2025-04-08 14:05:46 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2033281993  

I don't think this works like this. If I understood `CXX_MODULE_STD` correctly, this is a property of targets, not a global variable indicating whether import std is supported or not.  
  
To match the rest of Boost, I'd make `import std` the default when `BOOST_USE_MODULES` is defined. Then, if the user sets some CMake option (like `BOOST_PFR_DISABLE_IMPORT_STD`) fall back to includes. Since stdlib is the main source of compile times, I'd expect most of the people using modules because of compile times to also use import std.  
  
You need to test these workflows to avoid this kind of errors. We commonly test installing the project and consuming it with `find_package`, and consuming the project directly via `add_subdirectory`. Have a look at the mp11 CI workflows for examples.

> Username: apolukhin  
> Created_at: 2025-04-11 15:42:52 UTC  
> Updated_at: 2025-04-11 15:42:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2039819903  

You are right, I have to check the `CMAKE_CXX_COMPILER_IMPORT_STD` variable instead of the `CXX_MODULE_STD ` property

---

📁 CMakeLists.txt

```diff
  17 |-     add_subdirectory(module)
  23 |+     target_compile_definitions(boost_pfr PUBLIC BOOST_USE_MODULES)
  24 |+     target_include_directories(boost_pfr PUBLIC ${CMAKE_CURRENT_LIST_DIR}/include)
```

> Username: anarthal  
> Created_at: 2025-04-08 14:06:20 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2033283204  

This should be just `include`, rather than `${CMAKE_CURRENT_LIST_DIR}/include`


📁 test/core/run/core17_generated.cpp

```diff
  26 | int main() {
  27 |- #if BOOST_PFR_USE_CPP17
  27 |+ #if BOOST_PFR_USE_CPP17 && !defined(BOOST_USE_MODULES)  // TODO: fix for BOOST_USE_MODULES
```

> Username: anarthal  
> Created_at: 2025-04-08 14:08:03 UTC  
> Updated_at: 2025-04-08 14:29:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2033286786  

what's the problem with this one?

> Username: apolukhin  
> Created_at: 2025-04-10 14:17:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2037537416  

It uses `boost::pfr::detail` that is an implentation detail and is not exported from module

> Username: anarthal  
> Created_at: 2025-04-10 19:09:56 UTC  
> Url: https://github.com/boostorg/pfr/pull/196#discussion_r2038122981  

Ah! I see. I've workarounded these in Charconv by just using includes. It may make sense to leave them as you did, too. I tried creating an internal-only module that exported the detail functions to test, but the approach doesn't map very well to CMake, and it tends to either end up creating an extra static library that ends up getting installed (not good) or just refusing to build at all.


---
