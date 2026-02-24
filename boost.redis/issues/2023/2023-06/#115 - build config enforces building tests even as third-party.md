# #115 - build config enforces building tests even as third-party [Closed]

> Username: hexorer  
> Created at: 2023-06-20 11:32:27 UTC  
> Updated at: 2023-07-01 11:14:44 UTC  
> Closed at: 2023-07-01 11:14:44 UTC  
> Url: https://github.com/boostorg/redis/issues/115  

Currently in cmake file, building tests is enforced, and unlike many other common libraries tests are not guarded by a cmake option variable.  
  
This makes it hard to use as a third-party library, specially as it's quite slow.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-06-20 12:19:05 UTC  
> Url: https://github.com/boostorg/redis/issues/115#issuecomment-1598666528  

Hi, I will have a look on how to disable building the tests and the examples. Notice however that to use Boost.Redis you have to add the include path and `#include <boost/redis/src.hpp>` in one of your cpp files.  
  
Regarding compilations times: I am about to merge a branch that reduces it by close to 70%.

---

## Comment 2

> Username: hexorer  
> Created at: 2023-06-20 14:37:58 UTC  
> Url: https://github.com/boostorg/redis/issues/115#issuecomment-1598920145  

I can simply add something similar to the following (from libfmt).  
  
options for particular targets/features/etc.  
```cmake  
# Options that control generation of various targets.  
option(FMT_DOC "Generate the doc target." ${FMT_MASTER_PROJECT})  
option(FMT_INSTALL "Generate the install target." ON)  
option(FMT_TEST "Generate the test target." ${FMT_MASTER_PROJECT})  
```  
  
putting the related section under the condition.  
```cmake  
if (FMT_TEST)  
  enable_testing()  
  add_subdirectory(test)  
endif ()  
```  
  
and have the default build options enabled depending on if it's being built directly or has been included via add_subdirectory as a third-party:  
```cmake  
# Determine if fmt is built as a subproject (using add_subdirectory)  
# or if it is the master project.  
if (NOT DEFINED FMT_MASTER_PROJECT)  
  set(FMT_MASTER_PROJECT OFF)  
  if (CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)  
    set(FMT_MASTER_PROJECT ON)  
    message(STATUS "CMake version: ${CMAKE_VERSION}")  
  endif ()  
endif ()  
```  
  
looking at the root cmake of this project, the following sections seem to need an option:  
 - tests  
 - coverage  
 - examples  
 - docs  
 - benchmarks  
 - install (including packing)  
  
I can make a PR if the above approach is okay.

---

## Comment 3

> Username: mzimbres  
> Created at: 2023-06-20 18:12:11 UTC  
> Url: https://github.com/boostorg/redis/issues/115#issuecomment-1599277574  

A PR would be welcome. I think there needs to be no option for coverage as that is better handled in the preset. Have a look at https://github.com/boostorg/redis/blob/607946f00e62f4391bc67be6b3e96ebbe63dfc14/CMakePresets.json#L25.

---

## Comment 4

> Username: mzimbres  
> Created at: 2023-07-01 11:04:39 UTC  
> Updated at: 2023-07-01 11:04:55 UTC  
> Url: https://github.com/boostorg/redis/issues/115#issuecomment-1615850874  

Merged, thanks for your contribution. Can we close the ticket?
