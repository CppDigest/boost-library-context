# #1106 CMake boost super-build fixups for cmake-3.8 [Merged]

> Username: nigels-com  
> Created at: 2025-09-06 22:08:23 UTC  
> Updated at: 2025-09-11 13:28:28 UTC  
> Merged at: 2025-09-11 13:26:40 UTC  
> Closed at: 2025-09-11 13:26:40 UTC  
> Url: https://github.com/boostorg/json/pull/1106  

I've been on a little bit of an adventure to see what version of cmake is required for building boost.  
See [boostorg/boost#1081](https://github.com/boostorg/boost/pull/1081)  
  
Json needs cmake-3.15.0, otherwise it errors with:  
  
```  
-- Configuring done  
CMake Error at libs/json/test/CMakeLists.txt:63 (target_compile_options):  
  Error evaluating generator expression:  
  
    $<CXX_COMPILER_ID:GNU,Clang,AppleClang>  
  
  $<CXX_COMPILER_ID> expression requires one or zero parameters.  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-09-06 22:14:51 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3263251812  

An automated preview of the documentation is available at [https://1106.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1106.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-09-06 23:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3263278403  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1106/pullrequest-condensed-b185e14.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1106/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1106/pullrequest.html)

---

## Comment 3

> Username: Flamefire  
> Created_at: 2025-09-07 08:37:39 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3263591476  

I can confirm this change is sound: https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#genex:CXX_COMPILER_ID  
> Changed in version 3.15: Multiple compiler_ids can be specified. CMake 3.14 and earlier only accepted a single compiler ID.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2025-09-09 13:28:38 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3270756567  

To be honest, If that's all, I'm more willing to replace that one line with 3. I can do it myself, but if you want you can change the PR.

---

## Comment 5

> Username: nigels-com  
> Created_at: 2025-09-10 01:25:00 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3272873922  

I've updated the change here to support cmake-3.14 (instead of cmake-3.15).  
  
However cmake-3.13 cmake fails with `-DBUILD_TESTING=Y` due to parser requiring cmake-3.14:  
  
```  
$ PATH=/opt/cmake-3.13.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_COMPILER_LAUNCHER=ccache -DBUILD_TESTING=Y -DCMAKE_CXX_COMPILER=clang++-18 -DBUILD_TESTING=Y -DBOOST_INCLUDE_LIBRARIES=json && ninja -v  
-- Boost: using system layout: include, bin, lib, lib/cmake  
-- Boost: Release build, static libraries, MPI OFF, Python OFF, testing Y  
-- Boost: libraries included: json  
-- Boost.Charconv: quadmath support OFF  
-- Boost.Context: architecture x86_64, binary format elf, ABI sysv, assembler gas, suffix .S, implementation fcontext  
-- Boost.Fiber: NUMA target OS is linux  
-- Boost.Iostreams: ZLIB ON, BZip2 ON, LZMA ON (multithreaded), Zstd ON  
-- Found the following ICU libraries:  
--   data (required)  
--   i18n (required)  
--   uc (required)  
-- Found ICU: /usr/include (found suitable version "74.2", minimum required is "4.8.1")   
-- Boost.Locale: iconv ON, ICU ON, POSIX ON, std ON, winapi OFF  
-- Boost.Math: standalone mode OFF  
-- Boost.Multiprecision: standalone mode OFF  
CMake Error at libs/parser/CMakeLists.txt:9 (cmake_minimum_required):  
  CMake 3.14 or higher is required.  You are running version 3.13.0  
  
  
-- Configuring incomplete, errors occurred!  
```

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-09-10 01:29:57 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3272880729  

An automated preview of the documentation is available at [https://1106.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1106.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-09-10 02:32:00 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3272978052  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1106/pullrequest-condensed-89d531a.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1106/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1106/pullrequest.html)

---

## Comment 8

> Username: Flamefire  
> Created_at: 2025-09-10 08:14:21 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3273835176  

> I've updated the change here to support cmake-3.14 (instead of cmake-3.15).  
  
Why? It doesn't really matter what dependencies require. They state their requirements on their own, it is not the responsibly of this library.  
  
How does it get pulled in though? I don't see it as a [dependency](https://pdimov.github.io/boostdep-report/develop/json.html). Try `-DBoost_DEBUG=ON` for more info

---

## Comment 9

> Username: grisumbras  
> Created_at: 2025-09-10 10:13:23 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3274271426  

JSON definitely does not depend on Parser.

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2025-09-10 11:03:45 UTC  
> Updated_at: 2025-09-11 13:26:59 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3274451775  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1106?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.71%. Comparing base ([`e280e70`](https://app.codecov.io/gh/boostorg/json/commit/e280e702aad645075bc9137d9cff13892756d50e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`fa1223a`](https://app.codecov.io/gh/boostorg/json/commit/fa1223af8540e99c96a30aa7f9421d27b6613c7b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1106/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1106?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1106   +/-   ##  
========================================  
  Coverage    93.71%   93.71%             
========================================  
  Files           91       91             
  Lines         9156     9156             
========================================  
  Hits          8581     8581             
  Misses         575      575             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1106?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1106?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e280e70...fa1223a](https://app.codecov.io/gh/boostorg/json/pull/1106?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 11

> Username: nigels-com  
> Created_at: 2025-09-10 11:27:34 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3274532154  

The superproject cmake does seem to pulling in Parser via a GLOB in [BoostRoot.cmake](https://github.com/boostorg/cmake/blob/develop/include/BoostRoot.cmake#L244).  
  
cmake-3.13 does succeed with `-DBOOST_EXCLUDE_LIBRARIES=parser` specified to skip parser specifically.

---

## Comment 12

> Username: Flamefire  
> Created_at: 2025-09-10 12:28:22 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3274737605  

> The superproject cmake does seem to pulling in Parser via a GLOB in [BoostRoot.cmake](https://github.com/boostorg/cmake/blob/develop/include/BoostRoot.cmake#L244).  
>   
> cmake-3.13 does succeed with `-DBOOST_EXCLUDE_LIBRARIES=parser` specified to skip parser specifically.  
  
That glob is only for detecting available libraries. Libraries are only configured as requested. One exception is if `BUILD_TESTING` is set which adds all libraries but doesn't build them by default:  
https://github.com/boostorg/cmake/blob/81b08178c629b94b67e3baff5b1b9bee25db7f3a/include/BoostRoot.cmake#L355-L378

---

## Comment 13

> Username: grisumbras  
> Created_at: 2025-09-10 12:40:56 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3274800359  

https://drone.cpp.al/boostorg/json/1913/22/2  
  
This job invokes `cmake -DBoost_VERBOSE=1 -DBUILD_TESTING=ON -DCMAKE_INSTALL_PREFIX=iprefix -DBOOST_INCLUDE_LIBRARIES=json ..`, which results in the following (abriged) output:  
```  
...  
-- Boost: using system layout: include, bin, lib, lib/cmake  
-- Boost: using CMake 3.18.4  
-- Boost: Release build, static libraries, MPI OFF, Python OFF, testing ON  
-- Boost: libraries included: json  
-- Adding Boost dependency align  
-- Adding Boost dependency assert  
-- Adding Boost dependency config  
-- Adding Boost dependency container  
...  
-- Adding Boost dependency container_hash  
-- Adding Boost dependency core  
-- Adding Boost dependency describe  
-- Adding Boost dependency endian  
-- Adding Boost dependency headers  
-- Adding Boost dependency intrusive  
-- Adding Boost library json  
-- Adding Boost dependency move  
-- Adding Boost dependency mp11  
-- Adding Boost dependency predef  
-- Adding Boost dependency static_assert  
-- Adding Boost dependency system  
-- Adding Boost dependency throw_exception  
-- Adding Boost dependency variant2  
-- Adding Boost dependency winapi  
-- Configuring done  
```  
  
No Parser. Your result could be a consequence of `-DBUILD_TESTING=Y` not being parsed correctly.

---

## Comment 14

> Username: Flamefire  
> Created_at: 2025-09-10 15:19:39 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3275441850  

> No Parser. Your result could be a consequence of `-DBUILD_TESTING=Y` not being parsed correctly.  
  
Actually that is expected right now: The dependency scanner doesn't parse the test CML but just adds all other libraries with EXCLUDE_FROM_ALL and w/o installation set. But it still does add them.  
See https://github.com/boostorg/cmake/pull/86

---

## Comment 15

> Username: grisumbras  
> Created_at: 2025-09-11 10:40:27 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3279850261  

https://drone.cpp.al/boostorg/json/1916/22/2 <- this passes, which means JSON should work with CMake 3.8. However, according to https://github.com/boostorg/boost/pull/1083, the minimum should be 3.9. Can you change the PR to use that?

---

## Comment 16

> Username: nigels-com  
> Created_at: 2025-09-11 11:46:06 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3280164837  

@grisumbras Yes, happy to make that change to cmake-3.9.

---

## Comment 17

> Username: grisumbras  
> Created_at: 2025-09-11 11:49:41 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3280181804  

Scratch that, according to https://github.com/boostorg/boost/pull/1083 3.8 should work. So, please keep 3.8.

---

## Comment 18

> Username: Flamefire  
> Created_at: 2025-09-11 11:50:24 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3280185393  

> However, according to [boostorg/boost#1083](https://github.com/boostorg/boost/pull/1083), the minimum should be 3.9.  
  
Turns out that was a documentation bug and 3.8.0 does work for JSON.

---

## Comment 19

> Username: nigels-com  
> Created_at: 2025-09-11 12:24:28 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3280355932  

There was one more fixup needed for cmake-3.8.  
The `find_package(BoostPrettyPrinters)` portion requires cmake-3.12, but it seems optional.

---

## Comment 20

> Username: nigels-com  
> Created_at: 2025-09-11 12:27:17 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3280369559  

Tested in combination with [boostorg/cmake#86](https://github.com/boostorg/cmake/pull/86)  
  
```  
$ PATH=/opt/cmake-3.8.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_COMPILER_LAUNCHER=ccache -DBUILD_TESTING=N -DCMAKE_CXX_COMPILER=clang++-18 -DBUILD_TESTING=Y -DBOOST_INCLUDE_LIBRARIES=json && ninja check  
-- The CXX compiler identification is Clang 18.1.3  
-- Check for working CXX compiler: /usr/bin/clang++-18  
-- Check for working CXX compiler: /usr/bin/clang++-18 -- works  
...  
-- Generating done  
-- Build files have been written to: /home/nigels/dev/boost/build  
[69/69] cd /home/nigels/dev/boost/build && /opt/cmake-3.8.0-Linux-x86_64/bin/ctest --output-on-failure --no-tests=error -C Release  
Test project /home/nigels/dev/boost/build  
    Start 1: boost_json-tests  
1/4 Test #1: boost_json-tests ...................   Passed    0.87 sec  
    Start 2: boost_json-limits  
2/4 Test #2: boost_json-limits ..................   Passed    0.00 sec  
    Start 3: boost_json-no_exceptions  
3/4 Test #3: boost_json-no_exceptions ...........   Passed    0.00 sec  
    Start 4: boost_json-intrusive-macro-tests  
4/4 Test #4: boost_json-intrusive-macro-tests ...   Passed    0.00 sec  
  
100% tests passed, 0 tests failed out of 4  
```

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2025-09-11 12:31:29 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3280395358  

An automated preview of the documentation is available at [https://1106.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1106.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 22

> Username: grisumbras  
> Created_at: 2025-09-11 13:28:28 UTC  
> Url: https://github.com/boostorg/json/pull/1106#issuecomment-3280688230  

Thank you for your contribution.

---
