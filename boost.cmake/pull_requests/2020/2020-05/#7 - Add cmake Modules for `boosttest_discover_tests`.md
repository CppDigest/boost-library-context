# #7 Add cmake Modules for `boosttest_discover_tests` [Open]

> Username: DenizThatMenace  
> Created at: 2020-05-22 16:59:31 UTC  
> Updated at: 2026-01-14 13:07:39 UTC  
> Url: https://github.com/boostorg/cmake/pull/7  

I created a CMake script module which implements a `boosttest_discover_tests` CMake function, similar to the one provided by CMake for GTest.  
  
It also implements the new `PRE_TEST`behavior that exists for GTest which will be released with the next CMake release (3.18).

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-05-22 17:59:58 UTC  
> Url: https://github.com/boostorg/cmake/pull/7#issuecomment-632831446  

I'm not sure how this is going to work. How are people going to use these modules? They aren't going to be shipped with the Boost.Test package as installed by `apt install libboost-test` for instance.  
  
Ideally, after `find_package(Boost REQUIRED unit_test_framework)`, the modules would become available, somehow. But we currently have no mechanism of achieving this.

---

## Comment 2

> Username: DenizThatMenace  
> Created_at: 2020-05-22 18:13:48 UTC  
> Url: https://github.com/boostorg/cmake/pull/7#issuecomment-632836476  

Ultimately they should probably somehow be installed together with Boost.Test.  
  
But for now they would be in the Boost-source package and need to be copied by hand.

---

## Comment 3

> Username: DenizThatMenace  
> Created_at: 2020-05-24 22:25:41 UTC  
> Updated_at: 2020-05-24 22:25:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/7#issuecomment-633309565  

I just fixed some small errors.

---

## Comment 4

> Username: DenizThatMenace  
> Created_at: 2021-08-12 12:57:35 UTC  
> Url: https://github.com/boostorg/cmake/pull/7#issuecomment-897617087  

I rewrote the original implementation and made it compatible with older CMake versions.  
  
This `boosttest_discover_tests` implementation was inspired by the `gtest_discover_tests` implementation for *GoogleTest* that comes with CMake.    
I reached out to the original authors and contributors of `gtest_discover_tests` and asked them to dual-license their work also under the Boost Software License. They kindly agreed!    
Therefore I now got permission to distribute the implementation of `boosttest_discover_tests` under the Boost Software License as well.  
  
So, at least the license should not be a problem to integrate it with Boost.  
  
---  
  
@pdimov Regarding your question how these modules can be used by users of *Boost.Test*:  
  
1. I think the first step would be to just distribute these files together with the Boost source releases. (As I already said, interested users could just grab and copy these files into their own project.) That is mainly done by this pull-request.  
  
2. As a second step it should not be too difficult to make them available while building *Boost* using the new approach that uses CMake directly. (It is pretty much just a `list(APPEND CMAKE_MODULE_PATH "<path-to-this-Module-dir>")` call.)  
  
3. As a last step we should figure out a mechanism for installing these files together with the import-configuration files of *Boost.Test*. I do not think that will be too difficult.    
  From CMake that is just a simple `install(FILES...)` command (that should be associated with the *Boost.Test* component. (And I am sure that `b2` can do something similar.)    
  And when the import-configuration of *Boost.Test* is getting loaded by the user, that configuration just needs to provide a variable/property that should point to the directory containing these files. This directory then only has to be added to `CMAKE_MODULE_PATH` and the user has access to these files.

---

## Comment 5

> Username: ficzerepeti  
> Created_at: 2024-01-29 08:21:25 UTC  
> Url: https://github.com/boostorg/cmake/pull/7#issuecomment-1914182998  

Hi, I've come here to add my upvote to get this functionality included.  
[Similar approach could be taken to get these files discovered as for Catch2](https://github.com/catchorg/Catch2/blob/devel/docs/cmake-integration.md#automatic-test-registration).  
Is there more to this that I'm not seeing? I'm happy to help in case further work is needed.

---

## Comment 6

> Username: ahoarau  
> Created_at: 2026-01-14 13:07:39 UTC  
> Url: https://github.com/boostorg/cmake/pull/7#issuecomment-3749488838  

I confirm those modules are useful and working great 👍

---
