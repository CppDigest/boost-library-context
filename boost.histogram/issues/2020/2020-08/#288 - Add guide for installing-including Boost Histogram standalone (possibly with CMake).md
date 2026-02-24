# #288 - Add guide for installing/including Boost Histogram standalone (possibly with CMake) [Open]

> Username: dantrim  
> Created at: 2020-08-06 15:41:53 UTC  
> Updated at: 2020-11-08 22:22:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/288  

I am interested in replacing a `C++`  histogram implementation in an existing project with `boost/histogram`. This project does not depend on `boost`, nor does it necessarily intend to be run on machines that have any part of `boost` installed. For sake of simplicity, I would not like to add `boost` as an explicit dependency to this project.  
  
Given that `boost/histogram` is "header-only", what is the minimal installation procedure for allowing me to simply drop,  
```c++  
#include <boost/histogram.hpp>  
```  
at the top of a file and have everything work smoothly. I see that `boost/histogram` has internal dependencies on, e.g. `boost/nvp`, so just adding the include path to grab `boost/histogram.hpp` once checking out this repository is not sufficient.  
  
Is there a minimal working example of dropping in `boost/histogram` as a dependency to an already-existing cmake-based project? The docs are not clear on the installation (apart from assuming the normal perhaps-not-trivial boost installation procedures, I suppose) and I can really only find clear installation guidelines for the `python` bindings via `pip`.  
  
Thanks!

---

## Comment 1

> Username: henryiii  
> Created at: 2020-08-06 15:47:38 UTC  
> Updated at: 2020-08-06 18:18:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/288#issuecomment-670011231  

You can look at how boost-histogram in Python works - see https://github.com/scikit-hep/boost-histogram/tree/develop/extern to see what parts of boost are needed. As long as you are CMake and are okay with 3.11+, you can grab these via FetchContent instead of using submodules.

---

## Comment 2

> Username: dantrim  
> Created at: 2020-08-06 18:01:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/288#issuecomment-670087601  

Thanks @henryiii ,  
  
I don't intend to make the dependencies as sub-modules, so what I have is more or less in the below:  
  
```cmake  
if( ${FOUND_BOOST_HISTOGRAM} )  
   target_include_directories(${target_name} PUBLIC ${BOOST_HISTOGRAM_INCLUDE_DIR})  
   if(NOT ${FOUND_BOOST_HISTOGRAM_DEPS})  
        file(DOWNLOAD  
          "https://raw.githubusercontent.com/boostorg/cmake/develop/include/BoostFetch.cmake"  
          "${CMAKE_BINARY_DIR}/BoostFetch.cmake"  
        )  
        include("${CMAKE_BINARY_DIR}/BoostFetch.cmake")  
        boost_fetch(boostorg/cmake TAG develop NO_ADD_SUBDIR)  
  
        FetchContent_GetProperties(boostorg_cmake)  
        list(APPEND CMAKE_MODULE_PATH ${boostorg_cmake_SOURCE_DIR}/include)  
  
        foreach(boost_ext assert config core mp11 throw_exception variant2)  
              boost_fetch(boostorg/${boost_ext} TAG develop EXCLUDE_FROM_ALL)  
              FetchContent_GetProperties(boostorg_${boost_ext})  
              target_include_directories(${target_name) PUBLIC ${boostorg_${boost_ext}_SOURCE_DIR}/include)  
        endforeach(boost_ext)  
   endif()  
endif()  
```  
which will be put into a separate function/etc... This was mainly put together by following [boostorg's histogram CMakeLists.txt](https://github.com/boostorg/histogram/blob/develop/CMakeLists.txt).

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-08-10 13:45:15 UTC  
> Updated at: 2020-08-10 13:45:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/288#issuecomment-671363230  

Hi @dantrim   
I think what you have is a good solution, thank you for sharing. It is one of the possibilities that I would have suggested to you, but you already looked in the right place by yourself. From your message it is not clear whether this suits your needs or whether you would prefer a different approach? One remark: I would use a release tag instead of "develop" in `boost_fetch`, so that the build does not stop working all of a sudden, because we temporarily break something in the develop branch (that shouldn't happen very often thanks to our extensive and pedantic CI test suite, but still annoying for you if it does).  
  
We should add a guide for this to the docs and add a prominent link to that in the README.md.  
  
If you are comfortable with installing boost on your development machine at least and copying the headers of Boost Histogram into your repository, you can compile and use [bcp](https://github.com/boostorg/bcp) to do that. Be careful though, bcp will also extract the optional dependencies of Histogram by default, which is not what you want (some of these optional dependencies pull in a large part of Boost, e.g. Boost Serialization and Boost Range). You can suppress that by passing the right command-line options to bcp. Long story short, this boils down to placing a copy of all the required headers of Boost Histogram in your project. That's totally fine, but I would recommend to use `git submodule` instead, because that makes it easy to update.

---

## Comment 4

> Username: dantrim  
> Created at: 2020-08-11 15:26:42 UTC  
> Updated at: 2020-08-11 15:27:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/288#issuecomment-672015711  

Thanks @HDembinski   
  
I think I am happy with my approach -- obviously it is not a copy and paste from what I have in the above (I use a `find_package` approach, rather) but the above is generally the idea. I intend to use specific tags and I have added the `boost::histogram` package as a submodule to my package(s), yep.  
  
> We should add a guide for this to the docs and add a prominent link to that in the README.md.  
  
That would be great! To be honest, I can never get straight the best way to configure and install boost libraries since the boost docs typically leave them out in my experience. I think this is what makes some of my co-workers hesitant when I suggest adding a dependency on boost 👍 If you need a guinea pig to test things out let me know.

---

## Comment 5

> Username: HDembinski  
> Created at: 2020-08-11 17:28:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/288#issuecomment-672118444  

Thank you for the offer, I will come back to you about that.
