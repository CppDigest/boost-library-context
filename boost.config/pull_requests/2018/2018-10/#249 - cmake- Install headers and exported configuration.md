# #249 cmake: Install headers and exported configuration [Closed]

> Username: rleigh-codelibre  
> Created at: 2018-10-25 09:50:37 UTC  
> Updated at: 2020-10-19 13:30:26 UTC  
> Closed at: 2020-10-19 13:30:26 UTC  
> Url: https://github.com/boostorg/config/pull/249  

Initial work for https://github.com/boostorg/config/issues/247 which extends the existing two cmake files.  This makes it possible to install the headers with `make install` and it also builds two tests.  It additionally installs a configuration file so that you can run e.g.  
  
```  
find_package(BoostConfig VERSION 1.69 REQUIRED)  
```  
  
and then be able to use  
  
```  
target_link_libraries(foo Boost::config)  
```  
  
in any downstream project making use of Boost.Config.  The generated configuration is put into a "BoostConfigInternal" script, which is then sourced by a minimal "BoostConfig" script.  This is so that we can set additional variables for build-time configuration in this top-level script as follow-on work.  
  
Quite a few bits of the logic here could be generalised and reused by other Boost components as done by BCM.  However, for now I have kept it dead simple.  It's easy enough to refactor it later on to be more complex.  This is the minimal work needed to make it possible to use Boost::config in other projects.  
  
  
Hope this is useful,  
Roger

---

## Comment 1

> Username: rleigh-codelibre  
> Created_at: 2018-10-25 11:44:27 UTC  
> Url: https://github.com/boostorg/config/pull/249#issuecomment-433019137  

@Mike-Devel This might be of interest to you if you wanted to review it.

---

## Review 2 [Commented]

> Username: Mike-Devel  
> Created_at: 2018-10-25 13:39:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/249#pullrequestreview-168346651  

Thanks for inviting me.   
  
I made a few comments, but unfortunately, I'm not well versed in what the best practices for supporting installation in cmake are. My  files for header only libraries usually look much simpler, but I'm not sure if that is because they are missing some functionality or because this PR is more complicated than necessary.  
  
As there are probably a few bike-shedding decisions (naming, locations) here that should be consistent accross all boost libraries, it might be beneficial to get feedback from the boost mailing list or slack - or just wait if Robert Ramey's call for cmake submisions produces any results.

📁 CMakeLists.txt

```diff
  21 |+ file(GLOB_RECURSE boost_config_headers
  22 |+   RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}/include"
  23 |+   CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/include/boost/*")
```

> Username: Mike-Devel  
> Created_at: 2018-10-25 12:08:47 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228146203  

I think (haven't checked) CONFIGURE_DEPENDS only works in cmake 3.13 and later. That would make the file unusable on many systems.   
  
Do you really have to glob the headers and then install them one by one instead of just copying the whole directory in one step?

> Username: rleigh-codelibre  
> Created_at: 2018-10-25 13:56:30 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228182992  

It looks like `install(DIRECTORY …)` might work in this case.  Most other projects I've worked on had more complex arrangements where this didn't work.

---

📁 CMakeLists.txt

```diff
  53 |+         RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  54 |+         LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  55 |+         ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
```

> Username: Mike-Devel  
> Created_at: 2018-10-25 12:43:31 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228156572  

Do you actually need to specify those paths for a header-only library?

> Username: rleigh-codelibre  
> Created_at: 2018-10-25 13:57:19 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228183395  

They can probably be omitted for this specific library; they are they solely for completeness if used in other projects.

---

📁 CMakeLists.txt

```diff
  73 |+         COMPONENT "development")
  74 |+ 
  75 |+ add_subdirectory(test)
```

> Username: Mike-Devel  
> Created_at: 2018-10-25 13:39:50 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228176373  

At the moment, the purpose of the CMake file in test is not to run the unit tests, but to check, if the top level cmake file can be used from an external project (via the add_subdirectory process). As such the original order (`<root>/test/CMakeLists.txt` calls `<root>/CMakeLists.txt`) seems more appropriate.

> Username: rleigh-codelibre  
> Created_at: 2018-10-25 13:59:16 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228184226  

OK.  It's rather unusual, but I can revert this if that's how it's intended to be.


📁 test/CMakeLists.txt

```diff
  25 |- add_executable(config_test config_test.cpp)  
  26 |- target_link_libraries(config_test Boost::config)  
  21 |+ find_package(Threads)
```

> Username: Mike-Devel  
> Created_at: 2018-10-25 13:35:03 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228174652  

Why does this cmake self test have to enable multithreading?

> Username: rleigh-codelibre  
> Created_at: 2018-10-25 13:58:24 UTC  
> Updated_at: 2018-11-24 18:29:05 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r228183868  

The test requires it (fails with pthread error if omitted):  
  
```  
[ 25%] Linking CXX executable config_test  
/usr/bin/ld: CMakeFiles/config_test.dir/config_test.cpp.o: in function `boost_has_pthread_mutexattr_settype::f()':  
config_test.cpp:(.text+0x5ace): undefined reference to `pthread_mutexattr_init'  
/usr/bin/ld: config_test.cpp:(.text+0x5ae6): undefined reference to `pthread_mutexattr_settype'  
/usr/bin/ld: CMakeFiles/config_test.dir/config_test.cpp.o: in function `boost_has_pthread_yield::f()':  
config_test.cpp:(.text+0x5b15): undefined reference to `pthread_yield'  
/usr/bin/ld: CMakeFiles/config_test.dir/config_test.cpp.o: in function `boost_has_pthreads::test()':  
config_test.cpp:(.text+0x5b93): undefined reference to `pthread_mutex_trylock'  
/usr/bin/ld: config_test.cpp:(.text+0x5bd1): undefined reference to `pthread_create'  
/usr/bin/ld: config_test.cpp:(.text+0x5bed): undefined reference to `pthread_join'  
collect2: error: ld returned 1 exit status  
make[2]: *** [test/CMakeFiles/config_test.dir/build.make:84: test/config_test] Error 1  
make[1]: *** [CMakeFiles/Makefile2:93: test/CMakeFiles/config_test.dir/all] Error 2  
make: *** [Makefile:130: all] Error 2  
```


---

## Comment 3

> Username: rleigh-codelibre  
> Created_at: 2018-11-24 18:29:33 UTC  
> Url: https://github.com/boostorg/config/pull/249#issuecomment-441386761  

@Mike-Devel I've pushed some changes which should address the remainder of your comments.

---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2019-01-14 19:46:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/249#pullrequestreview-192330289  

📁 CMakeLists.txt

```diff
  16 | cmake_minimum_required(VERSION 3.5)
  16 |- project(BoostConfig LANGUAGES CXX)
  17 |+ project(BoostConfig VERSION 1.69 LANGUAGES CXX)
```

> Username: pdimov  
> Created_at: 2019-01-14 19:46:15 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r247632233  

I can pretty much guarantee that this will become incorrect if not tested. In fact it already is.

> Username: pdimov  
> Created_at: 2019-01-14 19:48:43 UTC  
> Updated_at: 2019-01-14 19:48:44 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r247633133  

See https://github.com/boostorg/assert/blob/develop/test/CMakeLists.txt#L6 and https://github.com/boostorg/assert/blob/develop/test/check_cmake_version.cpp for one possible approach.

> Username: rleigh-codelibre  
> Created_at: 2019-01-14 22:05:53 UTC  
> Url: https://github.com/boostorg/config/pull/249#discussion_r247677387  

Longer term, if the components are decoupled and usable independently, the versioning can then diverge between components.  
  
I'm not sure how the links you provided make a solution though.  Do you want to build and run a test program to get the version?  If so, would this not prove problematic for cross compilation?  
  
Would it not be possible to copy the logic here: https://github.com/Kitware/CMake/blob/master/Modules/FindBoost.cmake#L1370 so that we always use the version parsed directly from the headers?


---

## Comment 5

> Username: rleigh-codelibre  
> Created_at: 2020-10-19 13:30:26 UTC  
> Url: https://github.com/boostorg/config/pull/249#issuecomment-712157811  

I'm not doing work on this going forward.  If anyone wants to pick this up, please feel free to do so.  What's here is out of data and likely no longer useful.

---
