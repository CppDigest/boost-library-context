# #30 Implement initial version of C++20 module `boost.any` [Merged]

> Username: apolukhin  
> Created at: 2025-04-16 17:54:59 UTC  
> Updated at: 2025-05-12 14:54:45 UTC  
> Merged at: 2025-05-12 14:54:42 UTC  
> Closed at: 2025-05-12 14:54:42 UTC  
> Url: https://github.com/boostorg/any/pull/30  

`#include <boost/any...` is now implicitly does `import boost.any` if the modules are supported   
All the library internals now have unconditional module level linkage.  
  
Significant differences from https://anarthal.github.io/cppblog/modules3:  
* `BOOST_ANY_USE_STD_MODULE` macro switch for `import std;` / `includes` while building module. This allows to use module in C++20 and even without usable  `std` module.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-04-17 17:57:24 UTC  
> Url: https://github.com/boostorg/any/pull/30#issuecomment-2813674926  

@anarthal I'd appreciate comments on this PR on modules (as you proposed at https://github.com/boostorg/core/pull/191#issuecomment-2783677013)  
  
Note that this PR should also work well with modular `boost.type_index` without any additional changes (Boost.Any depends on Boost.TypeIndex). I'm quite excited to actually see, that modularization of libraries does not require modularization of dependencies first.

---

## Review 2 [Commented]

> Username: anarthal  
> Created_at: 2025-04-21 09:32:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/any/pull/30#pullrequestreview-2780846289  

📁 CMakeLists.txt

```diff
  10 |+     target_sources(boost_any PUBLIC
  11 |+         FILE_SET modules_public TYPE CXX_MODULES FILES
  12 |+             ${CMAKE_CURRENT_LIST_DIR}/modules/any.cppm
```

> Username: anarthal  
> Created_at: 2025-04-21 08:48:22 UTC  
> Updated_at: 2025-04-21 09:32:46 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2052145763  

Convention is `boost_any.cppm`


📁 modules/any.cppm

```diff
  32 |+ #endif
  33 |+ 
  34 |+ #include <boost/any.hpp>
```

> Username: anarthal  
> Created_at: 2025-04-21 09:21:08 UTC  
> Updated_at: 2025-04-21 09:32:46 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2052175574  

I _think_ (please check it) that, in module units, imports must be the first thing after the `export module` declaration (as opposed to other cpp files, where imports can be anywhere). This means that you _might_ need to add another ifdef in your `boost/type_index.hpp` files, so that you can perform the import directly here. Note that this does not apply for tests, because they are not module units.


📁 test/cmake_subdir_test/CMakeLists.txt

```diff
  48 |+ )
  49 |+ 
  50 |+ foreach (testsourcefile ${RUN_TESTS_SOURCES})
```

> Username: anarthal  
> Created_at: 2025-04-21 09:29:57 UTC  
> Updated_at: 2025-04-21 09:32:46 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2052185199  

I think this belongs to `test/CMakeLists.txt`, not to `test/cmake_subdir_test/CMakeLists.txt`. The current convention is that `cmake_subdir_test` tests how a user would consume your library by calling `add_subdirectory` to your project. It should contain a simple usage example, not the entire test suite.

> Username: apolukhin  
> Created_at: 2025-04-22 10:59:48 UTC  
> Updated_at: 2025-04-22 10:59:49 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2053875283  

I tried to to that at first, but failed to make it work.  
```  
mkdir build_module  
cd build_module  
cmake ../test  # fails to find dependencies  
```  
  
What is the recommended way to run such tests in CI?

> Username: anarthal  
> Created_at: 2025-04-22 17:05:11 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2054513153  

The usual workflow is  
  
```  
cd $BOOST_ROOT  
mkdir __build  
cd __build  
cmake -DBOOST_INCLUDE_LIBRARIES=any -DBUILD_TESTING=ON .. # add whatever flag you need here  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```  
  
So you configure and build the Boost superproject, and select your library with `BOOST_INCLUDE_LIBRARIES`.  
  
Another convention Peter uses (which you need for the above to work) is making test targets dependent on a `tests` convenience target in your CMake:  
  
```cmake  
    set_target_properties(boost_any_whatever_test PROPERTIES EXCLUDE_FROM_ALL ON)  
    add_dependencies(tests boost_any_whatever_test)  
```  
  
This guide has some other useful content: https://github.com/boostorg/cmake/blob/develop/developer.md

> Username: apolukhin  
> Created_at: 2025-04-29 16:52:03 UTC  
> Updated_at: 2025-04-29 16:52:27 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2066967020  

@anarthal nice! But with `-DBOOST_USE_MODULES=1` it prodices the following error:  
```  
CMake Error at tools/cmake/include/BoostInstall.cmake:300 (install):  
  install TARGETS target boost_any is exported but not all of its interface  
  file sets are installed  
Call Stack (most recent call first):  
  tools/cmake/include/BoostInstall.cmake:548 (boost_install_target)  
  tools/cmake/include/BoostRoot.cmake:187 (boost_install)  
  tools/cmake/include/BoostRoot.cmake:330 (__boost_auto_install)  
  CMakeLists.txt:20 (include)  
```

> Username: anarthal  
> Created_at: 2025-04-29 17:05:09 UTC  
> Updated_at: 2025-04-29 17:05:10 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2066985611  

Ah. This change in Boost.CMake should fix it: https://github.com/anarthal/boost-cmake/tree/feature/cxx20-modules  
  
If I have the time, I'll create a PR for Boost.CMake next week.

> Username: anarthal  
> Created_at: 2025-04-29 17:05:31 UTC  
> Updated_at: 2025-04-29 17:05:32 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2066986134  

So for now, it might be okay to keep it as you're doing it and change it later.

> Username: apolukhin  
> Created_at: 2025-04-29 18:38:22 UTC  
> Updated_at: 2025-04-29 18:38:23 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2067130813  

IMO it's worth merging the experimental modules functionality even if modules work only on a single compiler. There are enthusiasts and big companies that are interested in experimenting with modules

> Username: anarthal  
> Created_at: 2025-04-29 18:56:17 UTC  
> Updated_at: 2025-04-29 18:56:18 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2067157056  

I'm gonna try to move this forward along the next month.

---

📁 test/cmake_subdir_test/CMakeLists.txt

```diff
  43 |+ 
  44 |+     basic_any_test_mplif.cpp
  45 |+     basic_any_test_rv.cpp
```

> Username: anarthal  
> Created_at: 2025-04-21 09:32:41 UTC  
> Updated_at: 2025-04-21 09:32:46 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2052188669  

Looks like a subset of the test suite - are the other files causing trouble?

> Username: apolukhin  
> Created_at: 2025-04-22 11:00:42 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2053876578  

Other tests are `compile-fail` tests. What is the right way of writing those in CMake?

> Username: anarthal  
> Created_at: 2025-04-22 17:07:52 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2054517290  

Fair. You can do it, but it's not straightforward. AFAIK Peter has it in his Boost Test Jamfile: https://github.com/boostorg/cmake/blob/81b08178c629b94b67e3baff5b1b9bee25db7f3a/include/BoostTest.cmake#L189-L198  
  
In essence, you create a test that invokes cmake itself to build the target and assert that this fails.  
  
To be fair, I don't think it's worth the effort here.


---

## Review 3 [Commented]

> Username: anarthal  
> Created_at: 2025-04-21 15:41:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/any/pull/30#pullrequestreview-2781629557  

📁 CMakeLists.txt

```diff
  30 |+ else()
  31 |+     add_library(boost_any INTERFACE)
  32 |+     target_include_directories(boost_type_index INTERFACE include)
```

> Username: anarthal  
> Created_at: 2025-04-21 15:41:35 UTC  
> Url: https://github.com/boostorg/any/pull/30#discussion_r2052625243  

This should be `boost_any`


---
