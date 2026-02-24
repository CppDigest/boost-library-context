# #99 cmake: Add standalone build support for building and running unit tests [Merged]

> Username: nigels-com  
> Created at: 2025-09-08 09:36:42 UTC  
> Updated at: 2025-09-13 01:45:43 UTC  
> Merged at: 2025-09-13 01:45:43 UTC  
> Closed at: 2025-09-13 01:45:43 UTC  
> Url: https://github.com/boostorg/sort/pull/99  

The CMakeLists.txt currently supports the "superproject" boost build,  
but does not work stand alone.  
  
This change adds support for a stand-alone build of boost::sort, using the cmake method of detecting an existing boost installation.  
  
`-DBUILD_TESTING=Y` can be used for opting into the test coverage.  
  
The default `all` target will not build anything, since sort is a header-only library.  
The `tests` target can be used to build the unit tests.  
The `check` target can be used to run the unit tests, building if necessary.  
  
```  
$ PATH=/opt/cmake-3.10.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_COMPILER_LAUNCHER=ccache -DBUILD_TESTING=Y -DCMAKE_CXX_COMPILER=clang++-18 && ninja check  
-- Using cmake version 3.10.0  
-- Found Boost 1.89.0 at /opt/boost-1.89.0/lib/cmake/Boost-1.89.0  
--   Requested configuration: QUIET REQUIRED COMPONENTS core;range;included_test_exec_monitor  
-- BoostConfig: find_package(boost_headers 1.89.0 EXACT CONFIG REQUIRED QUIET HINTS /opt/boost-1.89.0/lib/cmake)  
-- Found boost_headers 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_headers-1.89.0  
-- BoostConfig: find_package(boost_core 1.89.0 EXACT CONFIG REQUIRED QUIET HINTS /opt/boost-1.89.0/lib/cmake)  
-- Found boost_core 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_core-1.89.0  
-- Found boost_assert 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_assert-1.89.0  
-- Found boost_config 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_config-1.89.0  
-- Found boost_static_assert 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_static_assert-1.89.0  
-- Found boost_throw_exception 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_throw_exception-1.89.0  
-- BoostConfig: find_package(boost_range 1.89.0 EXACT CONFIG REQUIRED QUIET HINTS /opt/boost-1.89.0/lib/cmake)  
-- Found boost_range 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_range-1.89.0  
-- Found boost_array 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_array-1.89.0  
-- Found boost_concept_check 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_concept_check-1.89.0  
-- Found boost_preprocessor 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_preprocessor-1.89.0  
-- Found boost_type_traits 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_type_traits-1.89.0  
-- Found boost_container_hash 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_container_hash-1.89.0  
-- Found boost_describe 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_describe-1.89.0  
-- Found boost_mp11 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_mp11-1.89.0  
-- Found boost_conversion 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_conversion-1.89.0  
-- Found boost_smart_ptr 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_smart_ptr-1.89.0  
-- Found boost_detail 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_detail-1.89.0  
-- Found boost_iterator 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_iterator-1.89.0  
-- Found boost_fusion 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_fusion-1.89.0  
-- Found boost_function_types 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_function_types-1.89.0  
-- Found boost_mpl 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_mpl-1.89.0  
-- Found boost_predef 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_predef-1.89.0  
-- Found boost_utility 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_utility-1.89.0  
-- Found boost_io 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_io-1.89.0  
-- Found boost_tuple 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_tuple-1.89.0  
-- Found boost_typeof 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_typeof-1.89.0  
-- Found boost_functional 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_functional-1.89.0  
-- Found boost_function 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_function-1.89.0  
-- Found boost_bind 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_bind-1.89.0  
-- Found boost_optional 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_optional-1.89.0  
-- Found boost_regex 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_regex-1.89.0  
-- BoostConfig: find_package(boost_included_test_exec_monitor 1.89.0 EXACT CONFIG REQUIRED QUIET HINTS /opt/boost-1.89.0/lib/cmake)  
-- Found boost_included_test_exec_monitor 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_included_test_exec_monitor-1.89.0  
-- Found boost_algorithm 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_algorithm-1.89.0  
-- Found boost_exception 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_exception-1.89.0  
-- Found boost_unordered 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_unordered-1.89.0  
-- Found boost_numeric_conversion 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_numeric_conversion-1.89.0  
-- Boost 1.85 found.  
-- Found Boost components:  
   core;range;included_test_exec_monitor  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/nigels/dev/boost/libs/sort/build  
[1/1] cd /home/nigels/dev/boost/libs/sort/build && /opt/cmake-3.10.0-Linux-x86_64/bin/ctest --output-on-failure -C Release  
Test project /home/nigels/dev/boost/libs/sort/build  
      Start  1: boost_sort_float_sort_test  
 1/11 Test  #1: boost_sort_float_sort_test .............   Passed    0.62 sec  
      Start  2: boost_sort_integer_sort_test  
 2/11 Test  #2: boost_sort_integer_sort_test ...........   Passed    0.03 sec  
      Start  3: boost_sort_sort_detail_test  
 3/11 Test  #3: boost_sort_sort_detail_test ............   Passed    0.00 sec  
      Start  4: boost_sort_string_sort_test  
 4/11 Test  #4: boost_sort_string_sort_test ............   Passed    0.08 sec  
      Start  5: boost_sort_test_block_indirect_sort  
 5/11 Test  #5: boost_sort_test_block_indirect_sort ....   Passed    0.89 sec  
      Start  6: boost_sort_test_flat_stable_sort  
 6/11 Test  #6: boost_sort_test_flat_stable_sort .......   Passed    0.21 sec  
      Start  7: boost_sort_test_insert_sort  
 7/11 Test  #7: boost_sort_test_insert_sort ............   Passed    0.02 sec  
      Start  8: boost_sort_test_parallel_stable_sort  
 8/11 Test  #8: boost_sort_test_parallel_stable_sort ...   Passed    0.97 sec  
      Start  9: boost_sort_test_pdqsort  
 9/11 Test  #9: boost_sort_test_pdqsort ................   Passed    0.14 sec  
      Start 10: boost_sort_test_sample_sort  
10/11 Test #10: boost_sort_test_sample_sort ............   Passed    0.90 sec  
      Start 11: boost_sort_test_spinsort  
11/11 Test #11: boost_sort_test_spinsort ...............   Passed    0.20 sec  
  
100% tests passed, 0 tests failed out of 11  
  
Total Test time (real) =   4.06 sec  
```

---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2025-09-08 10:54:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/sort/pull/99#pullrequestreview-3195965323  

Just had a quick look and pointed out enhancement from a the CMake perspective alone.  
  
I'd recommend waiting for the maintainer to decide if this use case should be supported. The current file is auto-generated

📁 test/CMakeLists.txt

```diff
  30 |+   # Follow the Boost convention: don't build test targets by default,
  31 |+   # and only when explicitly requested by building target tests
  32 |+   set_target_properties(${pname} PROPERTIES EXCLUDE_FROM_ALL ON)
```

> Username: Flamefire  
> Created_at: 2025-09-08 10:41:30 UTC  
> Updated_at: 2025-09-08 10:54:36 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2329855212  

Rather use `add_subdirectory(test EXCLUDE_FROM_ALL)`


📁 CMakeLists.txt

```diff
   8 | 
   8 |- project(boost_sort VERSION "${BOOST_SUPERPROJECT_VERSION}" LANGUAGES CXX)
   9 |+ cmake_minimum_required(VERSION 3.10...3.16)
```

> Username: Flamefire  
> Created_at: 2025-09-08 10:43:25 UTC  
> Updated_at: 2025-09-08 10:54:36 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2329859537  

You wrote  
>   it's find_package(Boost) that seems problematic prior to cmake-3.10.  
  
In which way? What is the issue?

> Username: nigels-com  
> Created_at: 2025-09-08 19:35:14 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2331186509  

```  
$ PATH=/opt/cmake-3.9.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_COMPILER_LAUNCHER=ccache -DBUILD_TESTING=Y -DCMAKE_CXX_COMPILER=clang++-18 && ninja check  
-- Using cmake version 3.9.0  
-- The CXX compiler identification is Clang 18.1.3  
...  
-- Found Boost 1.89.0 at /opt/boost-1.89.0/lib/cmake/Boost-1.89.0  
--   Requested configuration: QUIET REQUIRED COMPONENTS core;range;included_test_exec_monitor  
...  
-- Found boost_numeric_conversion 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_numeric_conversion-1.89.0  
Boost 1.85 found.  
Found Boost components:  
   core;range;included_test_exec_monitor  
CMake Error in /opt/cmake-3.9.0-Linux-x86_64/share/cmake-3.9/Modules/FindBoost.cmake:  
  cmake_policy PUSH without matching POP  
Call Stack (most recent call first):  
  CMakeLists.txt:22 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
```

> Username: Flamefire  
> Created_at: 2025-09-09 06:09:03 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2332120408  

I see, a bug introduced in CMake 3.9 and fixed in 3.9.1

---

📁 CMakeLists.txt

```diff
  19 |+ if(NOT BOOST_SUPERPROJECT_VERSION)
  20 |+   if(BUILD_TESTING)
  21 |+     set(Boost_DEBUG ON)
```

> Username: Flamefire  
> Created_at: 2025-09-08 10:44:10 UTC  
> Updated_at: 2025-09-08 10:54:36 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2329861039  

This is for users to set, not projects and produces log spam for everyone  
  
```suggestion  
```

> Username: nigels-com  
> Created_at: 2025-09-08 19:37:08 UTC  
> Updated_at: 2025-09-08 19:37:09 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2331189958  

I tend to prefer this much information about boost.  
cmake doesn't always do what's expected in terms of the version it found, or honoring `BOOST_ROOT`.

> Username: Flamefire  
> Created_at: 2025-09-09 06:10:35 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2332123209  

Yes **you** prefer this information. So you can use `-DBoost_DEBUG=ON` in the invocation of CMake  
  
With this you are forcing this on **every** user. And you/one usually only want this when something fails

> Username: nigels-com  
> Created_at: 2025-09-09 06:42:57 UTC  
> Updated_at: 2025-09-09 06:48:27 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2332192266  

More than that, I recommend it here in this mode.  These tests pull in a surprisingly large subset of boost, which I think that is relevant information in this context.  
  
Besides that `find_package(Boost)` is hit and miss in practice (maybe more-so for older boost and older cmake), it will log that it succeeded but doesn't actually make clear what version it's using, which can be an ODR concern.   In that sense failure is not always obvious via cmake failure.  
  
Perhaps log spam to you, but I'd very much prefer to see it in a bug report, than not.  
  
Boost is big, let's not pretend it doesn't have a bunch of moving parts.  
  
> -- Found boost_numeric_conversion 1.89.0 at /opt/boost-1.89.0/lib/cmake/boost_numeric_conversion-1.89.0  
> -- Boost 1.85 found.  
  
(Yeah thanks cmake for the misleading logging!)

---

📁 CMakeLists.txt

```diff
  24 |+       message(STATUS "Boost 1.85 (or later) is required")
  25 |+       return()
  26 |+     endif()
```

> Username: Flamefire  
> Created_at: 2025-09-08 10:44:31 UTC  
> Updated_at: 2025-09-08 10:54:36 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2329861798  

Superflous due to `REQUIRED`  
  
```suggestion  
```

---

📁 CMakeLists.txt

```diff
  18 |+ 
  19 |+ if(NOT BOOST_SUPERPROJECT_VERSION)
  20 |+   if(BUILD_TESTING)
```

> Username: Flamefire  
> Created_at: 2025-09-08 10:45:20 UTC  
> Updated_at: 2025-09-08 10:54:36 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2329863505  

Maybe rather combine this: `if(BUILD_TESTING AND NOT BOOST_SUPERPROJECT_VERSION)` which shows the intent more clearly, i.e. mirrors your sentence above  
  
And the whole thing should rather go to the test CML where it is required. This makes it easier to see which components are required. E.g. I see only `included_test_exec_monitor` being used

> Username: nigels-com  
> Created_at: 2025-09-08 21:31:22 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2331427141  

I think it's more conventional to have dependencies towards the start, more prominently than that.

---

📁 CMakeLists.txt

```diff
  43 | 
  44 |+ # Testing
  45 |+ 
```

> Username: Flamefire  
> Created_at: 2025-09-08 10:48:24 UTC  
> Updated_at: 2025-09-08 10:54:36 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2329869668  

I personally dislike such comments. `if(BUILD_TESTING` already provides this information so I see no added value.

---

📁 CMakeLists.txt

```diff
  16 |+ 
  17 |+ # Test coverage in stand-alone mode requires boost dependencies
  18 |+ 
```

> Username: Flamefire  
> Created_at: 2025-09-08 10:53:05 UTC  
> Updated_at: 2025-09-08 10:54:36 UTC  
> Url: https://github.com/boostorg/sort/pull/99#discussion_r2329879877  

Maybe remove this empty line to not disconnect it from the code block it applies  
  
```suggestion  
```


---
