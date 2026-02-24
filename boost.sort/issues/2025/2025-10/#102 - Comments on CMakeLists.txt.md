# #102 - Comments on CMakeLists.txt [Closed]

> Username: pdimov  
> Created at: 2025-10-19 10:05:17 UTC  
> Updated at: 2025-10-28 00:34:08 UTC  
> Closed at: 2025-10-28 00:34:08 UTC  
> Url: https://github.com/boostorg/sort/issues/102  

```  
message(STATUS "Using cmake version ${CMAKE_VERSION}")  
```  
You shouldn't unconditionally output status messages of this sort; do it only when your library is the root project. Think about what would happen if every library decides to output the CMake version; the user will get 200 messages telling him something he already knows.  
  
```  
cmake_minimum_required(VERSION 3.10...3.16)  
```  
What part requires CMake 3.10?  
  
```  
if(BOOST_SUPERPROJECT_VERSION)  
  project(boost_sort VERSION "${BOOST_SUPERPROJECT_VERSION}" LANGUAGES CXX)  
else()  
  project(boost_sort LANGUAGES CXX)  
endif()  
```  
The `if` is unnecessary and only makes things uglier. `project(boost_sort LANGUAGES CXX)` and `project(boost_sort VERSION "" LANGUAGES CXX)` are equivalent.  
  
```  
# Test coverage in stand-alone mode requires boost dependencies  
if(BUILD_TESTING AND NOT BOOST_SUPERPROJECT_VERSION)  
  set(Boost_DEBUG ON)  
  find_package(Boost 1.85 REQUIRED COMPONENTS core range included_test_exec_monitor)  
endif()  
```  
The condition is wrong; your library can be used via `add_subdirectory` without the superproject, in which case it shouldn't do this. (Add a `cmake_subdir_test` for this use case.) This should only be done when the library is the root project.  
  
```  
  if(NOT BOOST_SUPERPROJECT_VERSION)  
    include(CTest)  
    enable_testing()  
    add_custom_target(check COMMAND ${CMAKE_CTEST_COMMAND} --output-on-failure -C $<CONFIG>)  
    add_dependencies(check tests)  
  endif()  
```  
Same here; this should only be done when the library is the root project, not when it's a subproject.

---

## Comment 1

> Username: spreadsort  
> Created at: 2025-10-26 13:52:11 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3448563796  

I'd welcome a pull request; I only added this because you demanded it.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-10-26 14:37:12 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3448595163  

I'm sorry, you added what because I demanded it?

---

## Comment 3

> Username: spreadsort  
> Created at: 2025-10-27 01:31:35 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3449163275  

https://github.com/boostorg/sort/issues/94  
  
I don't use the cmake setup, except to test it when you said we needed to set it up.

---

## Comment 4

> Username: pdimov  
> Created at: 2025-10-27 01:45:07 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3449182338  

But all these changes are something you did in https://github.com/boostorg/sort/commit/de047e1250e973953242234e1f6145e840a2626a.

---

## Comment 5

> Username: pdimov  
> Created at: 2025-10-27 01:46:33 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3449184362  

Ah wait, it was @nigels-com who did that.

---

## Comment 6

> Username: nigels-com  
> Created at: 2025-10-27 02:03:06 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3449226111  

Happy to revisit that.

---

## Comment 7

> Username: pdimov  
> Created at: 2025-10-27 02:10:09 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3449237005  

What's the purpose of all these changes? `find_package(Boost REQUIRED COMPONENTS core range included_test_exec_monitor)` doesn't work on most Boost installs, so what's the use case?

---

## Comment 8

> Username: pdimov  
> Created at: 2025-10-27 02:11:48 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3449239554  

In fact, I don't think it works anywhere.

---

## Comment 9

> Username: nigels-com  
> Created at: 2025-10-27 10:24:08 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3450544473  

It worked for cmake build of boost, but I probably neglected to test the system-install (presumably b2 built) boost.

---

## Comment 10

> Username: pdimov  
> Created at: 2025-10-27 10:35:31 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3450592903  

A CMake-installed Boost might work this way, yes. I'll set up a test and submit a PR that preserves this use case.

---

## Comment 11

> Username: nigels-com  
> Created at: 2025-10-27 10:46:28 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3450647820  

> What's the purpose of all these changes?   
  
As I recall the motivation was to improve the completeness of the cmake build, to better support ongoing development.  Since boost::sort is header-only the default build doesn't compile anything, which does not serve to have confidence about proposed changes.  
  
Broadly speaking, to make cmake support a bit more first-class than it was before.  
Better, but not necessarily perfect.  
  
> What part requires CMake 3.10?  
  
cmake prior to 3.10 is deprecated, I didn't see much value in continued support for 3.5.  
(Such as actually testing the build with cmake 3.5).  
Later I came to understand that there is boost culture of maintaining support for very old tool chains, including cmake.  
There was some discussion a month or so ago, but I don't recall where that settled, in terms of a baseline.  
(I'd honestly be suprised if it's 3.10 or earlier, but I'll respect whatever was decided)  
  
> The if is unnecessary and only makes things uglier.  
  
I think it's a stylistic difference.  
We may well differ in terms of prioritising clarity and conciseness.  
I don't like referring to possibly undefined variables, smells bad to me.  
  
Perhaps the clarity could be improved by reordering the version info like so:  
  
```  
if(BOOST_SUPERPROJECT_VERSION)  
  project(boost_sort LANGUAGES CXX VERSION "${BOOST_SUPERPROJECT_VERSION}")  
else()  
  project(boost_sort LANGUAGES CXX)  
endif()  
```  
  
There is a recurring issue with cmake detecting an unintended boost version.  
It's quite reassuring to see this information in the log, in stand-along mode.  
A modest typo is enough for cmake to detect an entirely unintended version of boost, which is a cmake defect/flaw IMHO.  
  
```  
  set(Boost_DEBUG ON)  
```

---

## Comment 12

> Username: pdimov  
> Created at: 2025-10-27 12:45:07 UTC  
> Url: https://github.com/boostorg/sort/issues/102#issuecomment-3451103210  

https://github.com/boostorg/sort/pull/103
