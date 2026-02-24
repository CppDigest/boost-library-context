# #28 - Undefined symbols "boost::system::detail::generic_category_instance" [Closed]

> Username: ghost  
> Created at: 2018-11-28 05:28:31 UTC  
> Updated at: 2018-11-29 01:33:47 UTC  
> Closed at: 2018-11-29 01:33:47 UTC  
> Url: https://github.com/boostorg/system/issues/28  

I'm on macOS 10.14.1 with boost 1.68.0  
  
I built boost from source to try and see whether vcpkg was the source of the problem in my build (I submitted [an issue](https://github.com/Microsoft/vcpkg/issues/4829) to vcpkg for this.) I'm getting this when I link:  
  
```  
  "boost::system::detail::generic_category_instance", referenced from:  
      boost::system::generic_category() in log.cpp.o  
```  
  
Here's the build configuration in my cmake project, I'm also building for c++17:  
  
```  
ExternalProject_Add(  
  Boost  
  PREFIX "${CMAKE_BINARY_DIR}/extern"  
  GIT_REPOSITORY "https://github.com/boostorg/boost.git"  
  GIT_TAG "boost-1.68.0"  
  GIT_SHALLOW TRUE  
  GIT_PROGRESS TRUE  
  BUILD_IN_SOURCE TRUE  
  CONFIGURE_COMMAND   
    <SOURCE_DIR>/bootstrap.sh  
      --with-libraries=filesystem  
      --with-libraries=log  
      --with-libraries=locale  
      --with-libraries=system  
      --with-libraries=thread  
      --prefix=<SOURCE_DIR>  
  BUILD_COMMAND <SOURCE_DIR>/b2 install link=static variant=release threading=multi runtime-link=static  
  INSTALL_COMMAND ""  
)  
```

---

## Comment 1

> Username: ghost  
> Created at: 2018-11-28 05:40:22 UTC  
> Url: https://github.com/boostorg/system/issues/28#issuecomment-442326756  

I get the same error building 1.67.0, what am I doing wrong here?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-11-28 14:23:07 UTC  
> Url: https://github.com/boostorg/system/issues/28#issuecomment-442463726  

Try building Boost for C++14 or C++17 (I'm not sure what the default is for macOS 10, but it's typically C++03 for Clang.) To do that, add `cxxstd=14` or `cxxstd=17` to the b2 command line in BUILD_COMMAND.  
  
You can also try `GIT_TAG master` or `GIT_TAG boost-1.69.0-beta1` for the upcoming 1.69.0, in which Boost.System is header-only, which should sidestep this issue. But in general it's a good practice to build Boost for the same C++ standard you would be using to link to it.

---

## Comment 3

> Username: ghost  
> Created at: 2018-11-29 01:32:58 UTC  
> Updated at: 2018-11-29 01:33:45 UTC  
> Url: https://github.com/boostorg/system/issues/28#issuecomment-442671902  

I added `cxxstd=17` and it started linking, thanks for the help! I'll go ahead and close this because you've got a fix coming in the next version.
