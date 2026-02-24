# #48 - Consider providing a FindFit.cmake module [Closed]

> Username: ldionne  
> Created at: 2015-10-04 02:03:11 UTC  
> Updated at: 2015-10-06 17:00:56 UTC  
> Closed at: 2015-10-05 05:03:26 UTC  
> Url: https://github.com/boostorg/hof/issues/48  

I find it useful to have a FindXXX.cmake module for easily adding a library as a dependency in a CMake project. If you are interested in adding this, you can look at [Hana's](https://github.com/ldionne/hana/blob/bd974d6ad8be85fc1b7d73d206a756da2b5140cc/cmake/FindHana.cmake).

---

## Comment 1

> Username: viboes  
> Created at: 2015-10-04 15:27:33 UTC  
> Updated at: 2015-10-04 15:27:46 UTC  
> Url: https://github.com/boostorg/hof/issues/48#issuecomment-145358529  

I have not reached to run the test. Is the constraint CMAKE version 3 a real one?

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-10-05 05:03:26 UTC  
> Url: https://github.com/boostorg/hof/issues/48#issuecomment-145431160  

It already provides a pkg-config file.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-10-06 00:15:24 UTC  
> Url: https://github.com/boostorg/hof/issues/48#issuecomment-145704315  

Paul, how does the pkg-config file works? I'm on OS X; does that help me, and if so, how?

---

## Comment 4

> Username: pfultz2  
> Created at: 2015-10-06 17:00:56 UTC  
> Url: https://github.com/boostorg/hof/issues/48#issuecomment-145928111  

After you install Fit, you can run `pkg-config fit --cflags` for compiler flags and `pkg-config fit --libs` for linker flags(even on OS X). Inside of cmake you can just use the `FindPkgConfig` module:  
  
```  
find_package(PkgConfig)  
pkg_check_modules(MY_PKGS REQUIRED fit)  
```  
  
And then you can link your executable like this:  
  
```  
add_executable(myexe ${SOURCES})  
target_link_libraries(myexe ${MY_PKGS_LDFLAGS})  
target_include_directories(myexe PUBLIC ${MY_PKGS_INCLUDE_DIRS})  
target_compile_options(myexe PUBLIC ${MY_PKGS_CFLAGS_OTHER})  
```  
  
Right now, Fit is header only, so the `MY_PKGS_LDFLAGS` is not necessary, but if in the future Fit were to depend on another library that wasn't header-only, this would continue to work correctly, so its there for completeness.
