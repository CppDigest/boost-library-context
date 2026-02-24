# #56 Update CMake configuration to clarify supported build scenarios [Closed]

> Username: mloskot  
> Created at: 2020-03-12 22:52:48 UTC  
> Updated at: 2020-03-25 00:13:12 UTC  
> Closed at: 2020-03-25 00:07:31 UTC  
> Url: https://github.com/boostorg/json/pull/56  

Attempt to further clarify what build scenarios are supported by CMakeLists.txt  
  
------  
  
/cc @qis   
Sorry to bother you, but I am quite puzzled by this `out-of-tree` case. When this can happen exactly?  
  
```cmake  
else()  
    #  
    # Out-of-tree, non-standalone build  
    #  
    find_package(Boost COMPONENTS system)  
    target_link_libraries(boost_json  
        PUBLIC  
            Boost::system  
    )  
    option(BUILD_TESTING "Build the tests" ON)  
    if (BUILD_TESTING)  
        add_subdirectory(bench)  
        add_subdirectory(example)  
        add_subdirectory(test)  
    endif()  
  
endif()  
```  
  
If no standalone and no in-Boost is requested, then `elseif(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR` case happens.  
  
I must be missing something, otherwise this could become  
  
```cmake  
else()  
    message(FATAL_ERROR "Unsupported build scenario")  
endif()  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 00:04:26 UTC  
> Updated_at: 2020-03-17 20:24:25 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-598484553  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/56?src=pr&el=h1) Report  
> Merging [#56](https://codecov.io/gh/vinniefalco/json/pull/56?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/47bbedcd1cfcdd74d446f35d09b391dc7cc9a6ca?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/56/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/56?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #56   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4861     4861             
========================================  
  Hits          4817     4817             
  Misses          44       44  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/56?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/56?src=pr&el=footer). Last update [47bbedc...7377b13](https://codecov.io/gh/vinniefalco/json/pull/56?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: qis  
> Created_at: 2020-03-13 01:57:51 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-598511056  

@mloskot I'd assume it's for when you want to build the JSON library from trunk while using a stable Boost distribution for dependencies. STANDALONE also means no boost deps, which are sometimes wanted or even necessary.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-03-13 08:46:36 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-598613118  

@qis Makes sense, thanks. I will test that mode and update the PR

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-03-13 16:33:38 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-598809512  

Is there a possibility of changing one or more of the Travis (or Azure) matrix items to exercise these additional use cases?

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-03-14 00:40:10 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-598985233  

> while using a stable Boost distribution for dependencies.  
  
@qis Hmm, does this imply use of `cmake -DBOOST_ROOT=/where/boost/stable/install` ?  
  
I'm still trying to distinguish it from  
  
- `BOOST_JSON_FIND_BOOST`  
- `{boost-root}/libs/json` which uses stage build of Boost

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-03-14 01:15:38 UTC  
> Updated_at: 2020-03-14 01:17:08 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-598991475  

@vinniefalco   
> Is there a possibility of changing one or more of the Travis (or Azure) matrix items to exercise these additional use cases?  
  
I'm adding such builds for GitHub Actions   
- https://github.com/mloskot/boost-json/actions  
- https://github.com/mloskot/boost-json/tree/ml/add-github-actions  
  
I find workflows, each configured in its own `.yml` file, a much better structure than a single behemoth YAML full of conditions and mixture of bash scripts.  
  
Two notes:  
  
1. The superproject build is now no-op as I'm still unclear (https://github.com/vinniefalco/json/pull/56#issuecomment-598985233) what it is supposed to do  
2. The tests do not ran as blocked by #58

---

## Comment 7

> Username: qis  
> Created_at: 2020-03-14 03:48:26 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-599006202  

@mloskot Yes, if boost is installed using the system package manager, vcpkg or conan, then there is no `stage` directory. They can probably be combined, not sure.  
  
Perhaps it would make sense to use `find_package(… CONFIG)` here? But sometimes the generated configs don't work because they're not exported using CMake and rely on a specific b2 `layout` setting.  
  
I'd leave it alone or remove it until somebody comes along who needs it and opens an issue. Hopefully, Boost.JSON will be officially accepted into Boost soon - then it won't be needed at all.

---

## Comment 8

> Username: mloskot  
> Created_at: 2020-03-17 19:12:11 UTC  
> Updated_at: 2020-03-17 19:15:01 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-600249049  

I consider this one as finished for now, for the three of supported build scenarios, with corresponding GitHub Actions to test them.  
  
There are still two build scenarios that needs to be clarified and tested:  
1. CMake build as part of Boost superproject (see `TODO` comments, see [CMakeLists.txt of boostorg/move](https://github.com/boostorg/move/blob/61c09099f4d1de626c2b1c30c9b972abe3e27c9e/CMakeLists.txt#L8-L34) which may help to understand the comments)  
2. CMake build as out-of-tree, non-standalone build. This one is still a bit unclear, seems equivalent to `BOOST_JSON_FIND_BOOST` mode (see https://github.com/vinniefalco/json/pull/56#issuecomment-599006202).

---

## Comment 9

> Username: mloskot  
> Created_at: 2020-03-24 19:57:10 UTC  
> Url: https://github.com/boostorg/json/pull/56#issuecomment-603473470  

@vinniefalco This can be closed as you've already merged the commits manually.

---
