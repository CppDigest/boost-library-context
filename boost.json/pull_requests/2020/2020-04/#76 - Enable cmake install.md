# #76 Enable cmake install: [Closed]

> Username: madmongo1  
> Created at: 2020-04-22 11:58:15 UTC  
> Updated at: 2020-04-29 06:42:01 UTC  
> Closed at: 2020-04-29 06:42:01 UTC  
> Url: https://github.com/boostorg/json/pull/76  

When building outside of boost, boost_json now supports the install option of cmake.  
This patch produces a full, correct install which obeys CMAKE_INSTALL_PREFIX and  
creates the correct config.cmake files in order to construct the target Boost::json  
This config will automatically find Boost::system using the standard cmake find_package  
command

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-04-22 17:15:01 UTC  
> Url: https://github.com/boostorg/json/pull/76#issuecomment-617913226  

I have updated the generated config script so that standalone boost_json does not cause the Boost package to be found or linked against

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-04-22 17:45:50 UTC  
> Updated_at: 2020-04-28 11:46:32 UTC  
> Url: https://github.com/boostorg/json/pull/76#issuecomment-617930025  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/76?src=pr&el=h1) Report  
> Merging [#76](https://codecov.io/gh/CPPAlliance/json/pull/76?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/137c1653a8a8a7bb3b987ffa30f3ae4b0ac58f04&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/76/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/76?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #76   +/-   ##  
========================================  
  Coverage    98.95%   98.95%             
========================================  
  Files           57       57             
  Lines         5092     5092             
========================================  
  Hits          5039     5039             
  Misses          53       53             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/76?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/76?src=pr&el=footer). Last update [137c165...92c4f76](https://codecov.io/gh/CPPAlliance/json/pull/76?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-22 19:18:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/76#pullrequestreview-398501652  

📁 CMakeLists.txt

```diff
 126 |         PUBLIC
 127 |+             Boost::container
 128 |             Boost::system
```

> Username: vinniefalco  
> Created_at: 2020-04-22 19:18:53 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413253560  

Why do we link to system here but not on line 96

> Username: madmongo1  
> Created_at: 2020-04-22 20:11:00 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413294043  

it's linked on line 100


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2020-04-22 19:37:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/76#pullrequestreview-398517506  

📁 CMakeLists.txt

```diff
  60 |+ target_include_directories(boost_json PUBLIC
  61 |+         "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  62 |+         "$<INSTALL_INTERFACE:include>")
```

> Username: pdimov  
> Created_at: 2020-04-22 19:37:56 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413267394  

It's more idiomatic to use `include(GNUInstallDirs)` and then `${CMAKE_INSTALL_INCLUDEDIR}` here.


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2020-04-22 19:38:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/76#pullrequestreview-398518106  

📁 CMakeLists.txt

```diff
 125 |     target_link_libraries(boost_json
 126 |         PUBLIC
 127 |+             Boost::container
```

> Username: pdimov  
> Created_at: 2020-04-22 19:38:50 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413268144  

If this is going to link to `container`, the `find_package` probably also needs to be adjusted.

> Username: madmongo1  
> Created_at: 2020-04-22 20:10:26 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413293692  

These are not libraries that find_package would find. I assume they are created by the build super-project (I found the file like this)


---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2020-04-22 19:41:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/76#pullrequestreview-398519916  

📁 test/cmake_install_test/main.cpp

```diff
  10 |+ {
  11 |+     boost::json::error_code err = boost::json::error::expected_colon;
  12 |+     std::cout << err.message() << std::endl;
```

> Username: pdimov  
> Created_at: 2020-04-22 19:41:25 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413269849  

This doesn't exercise any functionality from the compiled library, so may hide link errors.

> Username: madmongo1  
> Created_at: 2020-04-22 20:12:39 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413295605  

isn't the compiled version just an artefact of trying to reduce travis build times? Will anyone actually use it?

> Username: vinniefalco  
> Created_at: 2020-04-22 21:00:17 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413329257  

No, the point of the compiled version is to reduce everyone's build time, by removing as much material as possible from the headers.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-22 19:41:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/76#pullrequestreview-398519934  

📁 test/cmake_install_test/CMakeLists.txt

```diff
  16 |+ add_test(NAME main COMMAND main)
  17 |+ 
  18 |+ add_custom_target(check COMMAND ${CMAKE_CTEST_COMMAND} --output-on-failure -C $<CONFIG>)
```

> Username: vinniefalco  
> Created_at: 2020-04-22 19:41:27 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413269876  

something is going on with your editor where the last line has no newline

> Username: madmongo1  
> Created_at: 2020-04-22 20:11:38 UTC  
> Updated_at: 2020-04-28 12:08:38 UTC  
> Url: https://github.com/boostorg/json/pull/76#discussion_r413294484  

I think my editor removes trailing newlines by default. I assumed this was normal.


---
