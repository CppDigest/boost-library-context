# #35 Feat: modernize CMake files [Closed]

> Username: ClausKlein  
> Created at: 2025-12-01 17:30:58 UTC  
> Updated at: 2026-01-01 07:55:51 UTC  
> Closed at: 2026-01-01 07:55:51 UTC  
> Url: https://github.com/boostorg/any/pull/35  

use [CMAKE_VERIFY_INTERFACE_HEADER_SETS](https://cmake.org/cmake/help/latest/variable/CMAKE_VERIFY_INTERFACE_HEADER_SETS.html) if possible  
  
This fix https://github.com/boostorg/any/issues/34 too  
  
But it depends on https://github.com/boostorg/cmake/pull/102!

---

## Review 1 [Commented]

> Username: ClausKlein  
> Created_at: 2025-12-01 20:54:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/any/pull/35#pullrequestreview-3526950009  

📁 modules/boost_any.cppm

```diff
  14 |+ 
  15 |+ // FIXME(CK): #include <boost/type_index.hpp>
  16 |+ import boost.type_index;
```

> Username: ClausKlein  
> Created_at: 2025-12-01 20:54:24 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2578616032  

@codeowners any reason to not use import?

> Username: apolukhin  
> Created_at: 2025-12-23 15:40:52 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2643582450  

`#include <boost/type_index.hpp>` automatically preprocesses to `import boost.type_index;` if the module is available

> Username: ClausKlein  
> Created_at: 2025-12-26 09:13:13 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2647865948  

But direct import is right and clear?


---

## Review 2 [Commented]

> Username: ClausKlein  
> Created_at: 2025-12-01 20:58:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/any/pull/35#pullrequestreview-3526966730  

📁 CMakeLists.txt

```diff
   5 |- cmake_minimum_required( VERSION 3.5...3.31 )
   6 |- project( boost_any VERSION "${BOOST_SUPERPROJECT_VERSION}" LANGUAGES CXX )
   5 |+ cmake_minimum_required(VERSION 3.21...4.2)
```

> Username: ClausKlein  
> Created_at: 2025-12-01 20:58:37 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2578631016  

@codeowners what is the minimum version that must be supported?  
  
`import std;` is usable starting with `cmake v3.30` AFAIK

> Username: anarthal  
> Created_at: 2025-12-03 09:29:38 UTC  
> Updated_at: 2025-12-03 09:37:09 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2584315060  

I don't think we want to bump the minimum version so much. These CMake files are invoked from the Boost superproject, so bumping a minimum version in one affects every other library. I think 3.8 is okay.

> Username: ClausKlein  
> Created_at: 2025-12-04 16:07:16 UTC  
> Updated_at: 2025-12-04 16:14:51 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2589680976  

on every build host `cmake v4.2` may be installed with `pipx install cmake`?  
  
I want to have `PROJECT_IS_TOP_LEVEL` available with `cmake version 3.21` anything older is not state of the art!


---

## Review 3 [Commented]

> Username: anarthal  
> Created_at: 2025-12-03 09:37:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/any/pull/35#pullrequestreview-3534049146  

📁 CMakeLists.txt

```diff
  30 |+     target_link_libraries(module_sample PRIVATE boost_any)
  31 | else()
  32 |+     set(CMAKE_VERIFY_INTERFACE_HEADER_SETS ON)
```

> Username: anarthal  
> Created_at: 2025-12-03 09:33:04 UTC  
> Updated_at: 2025-12-03 09:37:09 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2584327941  

CMake docs suggest to not set this directly in code (https://cmake.org/cmake/help/latest/variable/CMAKE_VERIFY_INTERFACE_HEADER_SETS.html)

> Username: ClausKlein  
> Created_at: 2025-12-04 16:09:03 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2589687137  

I changed it so it is only usable for standalone builds

---

📁 CMakeLists.txt

```diff
  27 |     set(__scope PUBLIC)
  28 |+ 
  29 |+     add_executable(module_sample modules/usage_sample.cpp)
```

> Username: anarthal  
> Created_at: 2025-12-03 09:34:53 UTC  
> Updated_at: 2025-12-03 09:37:09 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2584334434  

Please don't build examples from top-level cmakes

> Username: ClausKlein  
> Created_at: 2025-12-04 16:12:56 UTC  
> Url: https://github.com/boostorg/any/pull/35#discussion_r2589700431  

done


---

## Comment 4

> Username: apolukhin  
> Created_at: 2025-12-24 17:13:19 UTC  
> Url: https://github.com/boostorg/any/pull/35#issuecomment-3690266612  

Fails with error:  
```  
 CMake Error at /home/runner/work/any/boost-root/libs/any/CMakeLists.txt:25 (if):  
  if given arguments:  
  
    "IN_LIST" "CMAKE_CXX_COMPILER_IMPORT_STD"  
  
  Unknown arguments specified  
```

---
