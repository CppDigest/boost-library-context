# #170 Fix cmake and MSVC Compiler issues [Merged]

> Username: d5ch4k  
> Created at: 2024-04-26 11:13:32 UTC  
> Updated at: 2024-10-01 19:05:48 UTC  
> Merged at: 2024-10-01 05:47:53 UTC  
> Closed at: 2024-10-01 05:47:53 UTC  
> Url: https://github.com/boostorg/parser/pull/170  

MSVC in cmake has always been a boolean variable ( see https://cmake.org/cmake/help/latest/variable/MSVC.html)  
detecting the MSVC Compiler requires therefore  'if (MSVC)'  in CMakeLists.txt  
  
further, the MSVC Compiler will always emit '199711L' for __cplusplus, if it is called without the option '/Zc:__cplusplus'  
(see https://learn.microsoft.com/en-us/cpp/build/reference/zc-cplusplus?view=msvc-170 )  
  
this odd behavior will break any version check in the headers.   
e.g. './include/boost/parser/detail/text/detail/begin_end.hpp'  
  or 'boost/hana/traits.hpp'  
  
therefore 'add_compile_options(/Zc:__cplusplus)' is always required for the MSVC compiler

---

## Comment 1

> Username: tzlaine  
> Created_at: 2024-09-30 17:55:50 UTC  
> Url: https://github.com/boostorg/parser/pull/170#issuecomment-2383833537  

Thanks for doing this.  I only recently learned about `add_compile_options(/Zc:__cplusplus)`.   
 It is quite simply scandalous.  
  
I'm a little nervous about using `CMAKE_CXX_COMPILER_FRONTEND_VARIANT`, since a Google of it does not point to the usual CMake doc page, though there are definitely references to it in various places.  It appears to be undocumented -- is that right?  If so, I think I'd prefer an equivalent PR that just adds the missing variant(s) for `WIN32`.  If it is documented, and I just missed it, I'm all for the current approach, except:  
  
Please don't leave a block of commented-out CMake in your PR. :)

---

## Comment 2

> Username: d5ch4k  
> Created_at: 2024-09-30 21:44:39 UTC  
> Url: https://github.com/boostorg/parser/pull/170#issuecomment-2384201240  

If I type `CMAKE_CXX_COMPILER_FRONTEND_VARIANT` into google search, the first hit points to [https://cmake.org/cmake/help/latest/variable/CMAKE_LANG_COMPILER_FRONTEND_VARIANT.html](url), which I suppose is the official documentation.  
I oversaw that it was introduced in version 3.14 which makes the adjustment of `cmake_minimum_required(VERSION 3.8...3.20)`necessary.  
  
If this is a no-go, then  
```  
if (MSVC)  
    get_filename_component(CNAME "${CMAKE_CXX_COMPILER}" NAME)  
    message(STATUS "Detected MSVC style compiler frontend '" ${CNAME} "'")  
    add_definitions(/W3)  
    add_compile_options(/Zc:__cplusplus) # not sure, if it necessary for clang-cl  
else()  
    get_filename_component(CNAME "${CMAKE_CXX_COMPILER}" NAME)  
    message(STATUS "Detected GNU style compiler frontend '" ${CNAME} "'")  
    add_definitions(-Wall)  
endif ()  
```  
is the way to go (if we assume, there exist only 2 different command-line styles)  
  
Martin

---

## Comment 3

> Username: tzlaine  
> Created_at: 2024-09-30 23:16:48 UTC  
> Updated_at: 2024-09-30 23:17:09 UTC  
> Url: https://github.com/boostorg/parser/pull/170#issuecomment-2384372464  

Oh no I didn't mean you needed to revert it to an older-Cmake way of doing things.  3.14 is pretty old.  I was just worried that `CMAKE_CXX_COMPILER_FRONTEND_VARIANT` was an undocumented/internal variable.  Now that I see it's official, I'd like to use that version of your patch.  Sorry for the confusion.

---

## Comment 4

> Username: d5ch4k  
> Created_at: 2024-10-01 05:28:50 UTC  
> Url: https://github.com/boostorg/parser/pull/170#issuecomment-2384830988  

I've reverted it and updated the `cmake_minimum_required`lines  
  
Martin

---

## Comment 5

> Username: tzlaine  
> Created_at: 2024-10-01 05:47:50 UTC  
> Url: https://github.com/boostorg/parser/pull/170#issuecomment-2384854983  

Very nice!  Thanks again for doing this.

---
