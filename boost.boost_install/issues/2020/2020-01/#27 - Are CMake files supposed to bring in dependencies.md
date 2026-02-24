# #27 - Are CMake files supposed to bring in dependencies? [Open]

> Username: giomasce  
> Created at: 2020-01-01 16:26:09 UTC  
> Updated at: 2020-01-06 23:13:45 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27  

Consider this `CMakeLists.txt` file:  
```  
project(Boost CXX)  
cmake_minimum_required(VERSION 3.0)  
  
FIND_PACKAGE(Boost COMPONENTS thread REQUIRED)  
INCLUDE_DIRECTORIES (${Boost_INCLUDE_DIRS})  
  
ADD_EXECUTABLE(test test.cpp)  
TARGET_LINK_LIBRARIES(test ${Boost_LIBRARIES})  
```  
with this `test.cpp` file:  
```  
#include <boost/thread/condition_variable.hpp>  
  
int main() {  
    boost::condition_variable cv;  
    boost::mutex mutex;  
    boost::unique_lock<boost::mutex> lock(mutex);  
    cv.wait_for(lock, boost::chrono::seconds(1));  
    return 0;  
}  
```  
  
When I try to compile with `cmake .` and `make`, the compilation fails with  
```  
[100%] Linking CXX executable test  
/usr/bin/ld: CMakeFiles/test.dir/test.cpp.o: in function `boost::cv_status boost::condition_variable::wait_for<long, boost::ratio<1l, 1l> >(boost::unique_lock<boost::mutex>&, boost::chrono::duration<long, boost::ratio<1l, 1l> > const&)':  
test.cpp:(.text._ZN5boost18condition_variable8wait_forIlNS_5ratioILl1ELl1EEEEENS_9cv_statusERNS_11unique_lockINS_5mutexEEERKNS_6chrono8durationIT_T0_EE[_ZN5boost18condition_variable8wait_forIlNS_5ratioILl1ELl1EEEEENS_9cv_statusERNS_11unique_lockINS_5mutexEEERKNS_6chrono8durationIT_T0_EE]+0x15): undefined reference to `boost::chrono::steady_clock::now()'  
collect2: error: ld returned 1 exit status  
```  
Clearly `boost::condition_variable::wait_from` requires `boost::chrono::steady_clock::now`, but the latter is not automatically brought in as a dependency by CMake scripts. Should I expect CMake to automatically resolve dependencies towards other Boost libraries (i.e., this is a bug), or am I supposed to explicitly specify `chrono` in the `CMakeLists.txt` file?  
  
Thanks.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-01-01 16:34:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570065994  

This depends on whether your `find_package(Boost)` call is resolved by `FindBoost.cmake`, or with the CMake configuration files installed by Boost; more specifically, on what the variable `Boost_LIBRARIES` contains.  
  
The easiest way to fix that, regardless of the above, is to forego the use of variables and use the imported targets instead. That is, remove the line  
```  
INCLUDE_DIRECTORIES (${Boost_INCLUDE_DIRS})  
```  
and replace  
```  
TARGET_LINK_LIBRARIES(test ${Boost_LIBRARIES})  
```  
with  
```  
TARGET_LINK_LIBRARIES(test Boost::thread)  
```

---

## Comment 2

> Username: giomasce  
> Created at: 2020-01-01 17:04:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570067888  

I did what you say, but the result is the same (cannot the `chrono` library at linking time). For the record, the linking command is  
```  
/usr/bin/c++    -rdynamic CMakeFiles/test.dir/test.cpp.o  -o test /usr/lib/x86_64-linux-gnu/libboost_thread.so.1.71.0 -lpthread  
```  
I believe that what `find_package(Boost)` is doing is calling `FindBoost.cmake`, which in turn calls the files installed by Boost. However, I am not a CMake expert. BTW, the context is that I am installing the CMake scripts in the Debian packages for Boost. I am still working on version 1.71. Could it be that something has been fixed in the meantime?

---

## Comment 3

> Username: pdimov  
> Created at: 2020-01-01 17:09:01 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570068192  

If you configure with `-DBoost_VERBOSE=1`, or with `Boost_DEBUG=1`, what is the output?

---

## Comment 4

> Username: giomasce  
> Created at: 2020-01-01 17:13:19 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570068478  

```  
-- The CXX compiler identification is GNU 9.2.1  
-- Check for working CXX compiler: /usr/lib/ccache/c++  
-- Check for working CXX compiler: /usr/lib/ccache/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Boost 1.71.0 at /usr/lib/x86_64-linux-gnu/cmake/Boost-1.71.0  
--   Requested configuration: QUIET REQUIRED COMPONENTS thread  
-- BoostConfig: find_package(boost_headers 1.71.0 EXACT CONFIG REQUIRED QUIET HINTS /usr/lib/x86_64-linux-gnu/cmake)  
-- Found boost_headers 1.71.0 at /usr/lib/x86_64-linux-gnu/cmake/boost_headers-1.71.0  
-- BoostConfig: find_package(boost_thread 1.71.0 EXACT CONFIG REQUIRED QUIET HINTS /usr/lib/x86_64-linux-gnu/cmake)  
-- Found boost_thread 1.71.0 at /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.71.0  
-- Boost toolset is gcc9 (GNU 9.2.1)  
-- Scanning /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.71.0/libboost_thread-variant*.cmake  
--   Including /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.71.0/libboost_thread-variant-shared.cmake  
--   [x] libboost_thread.so.1.71.0  
--   Including /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.71.0/libboost_thread-variant-static.cmake  
--   [ ] libboost_thread.a  
-- Adding boost_thread dependencies: headers  
-- Found Boost: /usr/lib/x86_64-linux-gnu/cmake/Boost-1.71.0/BoostConfig.cmake (found version "1.71.0") found components:  thread   
-- Configuring done  
-- Generating done  
-- Build files have been written to: /tmp/test/build  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2020-01-01 17:19:45 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570068910  

Interesting. When I install locally on Windows, I do have Thread depending on Chrono. But on Linux it doesn't, according to both your log and Travis: https://travis-ci.org/boostorg/boost_install/jobs/629443426  
  
The difference is that Travis succeeds, but that's probably because I don't use condition_variable in my test: https://github.com/boostorg/boost_install/blob/develop/test/thread/quick.cpp  
  
I'll look into this.

---

## Comment 6

> Username: giomasce  
> Created at: 2020-01-01 17:24:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570069218  

Thanks. There is clearly some logic behind whether depending on `chrono` on not in the `thread`'s Jamfile, but I do not know enough of Boost.Build to get it.

---

## Comment 7

> Username: pdimov  
> Created at: 2020-01-01 17:30:19 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570069658  

The logic is fairly straightforward; on Pthread platforms (https://github.com/boostorg/thread/blob/develop/build/Jamfile.v2#L256) Chrono is not added as a dependency and `BOOST_THREAD_DONT_USE_CHRONO` is defined (https://github.com/boostorg/thread/blob/develop/build/Jamfile.v2#L271).  
  
On Windows, Chrono is added as a dependency (https://github.com/boostorg/thread/blob/develop/build/Jamfile.v2#L282) and `BOOST_THREAD_USES_CHRONO` is defined (https://github.com/boostorg/thread/blob/develop/build/Jamfile.v2#L281).  
  
Then in the Pthread implementation of condition_variable all Chrono uses are guarded by `BOOST_THREAD_USES_CHRONO`: https://github.com/boostorg/thread/blob/develop/include/boost/thread/pthread/condition_variable_fwd.hpp#L23  
  
What I don't understand is why your code compiles; you pass a Chrono duration to `cv.wait_for`, without including a Chrono header, and in any case this `wait_for` overload should have been ifdef-ed out.

---

## Comment 8

> Username: giomasce  
> Created at: 2020-01-01 17:51:14 UTC  
> Updated at: 2020-01-01 17:53:39 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570071017  

At https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/config.hpp#L143 `BOOST_THREAD_USES_CHRONO` is defined if `BOOST_THREAD_DONT_USE_CHRONO` is not defined. So the problem is that CMake should ensure that `BOOST_THREAD_DONT_USE_CHRONO` is defined.

---

## Comment 9

> Username: pdimov  
> Created at: 2020-01-01 17:58:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570071427  

Correct. I missed that in my search.  
  
Getting that define into the CMake config won't be easy though.

---

## Comment 10

> Username: giomasce  
> Created at: 2020-01-01 18:07:43 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570072032  

I have no idea of what is the best way forward here. In line of principle saying that `BOOST_THREAD_DONT_USE_CHRONO` is the default and not the other way around would be a solution, but maybe this would cause other breakages when the opposite is assumed. Or one could just always depend on `chrono`, and I have no idea how often this can be inconvenient in corner cases. For me there is no problem in working around the issue adding an explicit dependency on `chrono`.

---

## Comment 11

> Username: pdimov  
> Created at: 2020-01-01 18:12:20 UTC  
> Updated at: 2020-01-01 18:12:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570072309  

In fact, even if we did have code to properly move the defines from `usage-requirements` (the equivalent of `INTERFACE_COMPILE_DEFINITIONS`) of a library to the CMake configuration, this still wouldn't have worked, because the Thread Jamfile only defines `BOOST_THREAD_DONT_USE_CHRONO` in `requirements` (the equivalent of `COMPILE_DEFINITIONS`).  
  
The immediate workaround from user point of view would be to just link to Chrono explicitly when using overloads taking `chrono::duration`, which is not unreasonable. I wonder whether we don't have a deeper problem here though, because the library is still being built without `BOOST_THREAD_USES_CHRONO`. In this case everything seems to happen in the headers, but maybe other cases will still break.

---

## Comment 12

> Username: pdimov  
> Created at: 2020-01-01 18:15:58 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-570072526  

Maybe the best way to solve this is to just have Thread always depend on Chrono after all.

---

## Comment 13

> Username: xnox  
> Created at: 2020-01-06 23:13:45 UTC  
> Url: https://github.com/boostorg/boost_install/issues/27#issuecomment-571357036  

@giomasce whilst this is being resolved, in the cmake example i think we can explicitely request chrono component for the thread autopkgtest in Debian.
