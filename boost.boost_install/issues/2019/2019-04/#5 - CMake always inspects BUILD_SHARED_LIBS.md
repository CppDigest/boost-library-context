# #5 - CMake always inspects BUILD_SHARED_LIBS [Closed]

> Username: sophonet  
> Created at: 2019-04-27 09:26:59 UTC  
> Updated at: 2019-05-30 18:18:35 UTC  
> Closed at: 2019-05-18 19:33:41 UTC  
> Url: https://github.com/boostorg/boost_install/issues/5  

Currently, since boost 1.70.0, imported CMake targets for boost libraries inspect the BUILD_SHARED_LIBS variable and complain if its boost setting does not match the project's setting (and e.g. suggest to add an additional CMake variable Boost_USE_STATIC_LIBS=OFF in case BUILD_SHARED_LIBS is not ON).  
  
While the idea is nice and makes sense in some cases when building libraries, checks for BUILD_SHARED_LIBS only makes sense for CMake projects build libraries. They do not make sense for other projects that, for example, only build executables via ADD_EXECUTABLE().  
  
Hence, CMake projects for executables currently always need to be configured with an additional non-project-related variable Boost_USE_STATIC_LIBS such that FIND_PACKAGE(Boost) does not fail.  
  
Actually, I think it would be cleaner to skip the check on BUILD_SHARED_LIBS completely and leave it to the developer to ensure that he configures boost and derived projects consistently.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-18 19:33:41 UTC  
> Url: https://github.com/boostorg/boost_install/issues/5#issuecomment-493701593  

I dropped the reliance on `BUILD_SHARED_LIBS` in https://github.com/boostorg/boost_install/commit/a5dd3f68e15b023243190a69956791b5279f8e4e.

---

## Comment 2

> Username: tadeu  
> Created at: 2019-05-30 18:18:34 UTC  
> Url: https://github.com/boostorg/boost_install/issues/5#issuecomment-497430774  

For those on 1.70.0, you can temporarily `set(BUILD_SHARED_LIBS ON)` before `find_package(Boost ...)` as a workaround.
