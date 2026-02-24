# #796 - How to build Boost.Beast on linux? [Closed]

> Username: gregsifr  
> Created at: 2017-09-25 07:49:54 UTC  
> Updated at: 2019-08-12 18:34:47 UTC  
> Closed at: 2017-09-29 13:22:46 UTC  
> Url: https://github.com/boostorg/beast/issues/796  

I've looked at the [build instructions](https://github.com/boostorg/beast#building) and they are only for Windows.  
  
How to build this library in Linux?  
  
My friend posted the [question on Stack Overflow](https://stackoverflow.com/questions/46397297/how-to-build-boost-beast-on-linux-the-library-is-on-github-but-wont-be-include).  
  
Thank you

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-25 16:46:14 UTC  
> Url: https://github.com/boostorg/beast/issues/796#issuecomment-331941736  

The CMakeLists.txt only works for Windows, but the bjam build scripts work on all platforms. The docs could do a better job explaining this.  
  
However, note that Beast is header-only so you only need to use bjam or cmake if you are trying to build the examples or tests.

---

## Comment 2

> Username: zykis  
> Created at: 2018-01-31 17:16:12 UTC  
> Updated at: 2018-01-31 17:22:53 UTC  
> Url: https://github.com/boostorg/beast/issues/796#issuecomment-362003804  

Tried to use it as a standalone headers results in following:  
```  
~/projects/testBeast$ gcc -Wall main.cpp -o server  
In file included from /usr/include/boost/beast/core/file_base.hpp:14:0,  
                 from /usr/include/boost/beast/core/type_traits.hpp:14,  
                 from /usr/include/boost/beast/core/bind_handler.hpp:14,  
                 from /usr/include/boost/beast/core.hpp:15,  
                 from main.cpp:16:  
/usr/include/boost/beast/core/string.hpp:15:41: fatal error: boost/utility/string_view.hpp: No such file or directory  
```  
  
  
After that i've tried to put it into 1.62 boost headers, which results in:  
`boost/utility/string_view.hpp: No such file or directory`  
  
Any suggestions? Ubuntu 16.04

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-31 17:36:10 UTC  
> Url: https://github.com/boostorg/beast/issues/796#issuecomment-362009963  

Beast requires Boost 1.66.0. The only supported version of Beast is the one that is in Boost (in 1.66.0)

---

## Comment 4

> Username: zykis  
> Created at: 2018-01-31 18:03:26 UTC  
> Url: https://github.com/boostorg/beast/issues/796#issuecomment-362018369  

Yeah. It was just 1.66 with Beast within. And a sweet install script. Sorry for missleading

---

## Comment 5

> Username: Osteri  
> Created at: 2019-08-04 19:21:09 UTC  
> Url: https://github.com/boostorg/beast/issues/796#issuecomment-518029186  

Is there any particular reason why cmake builds aren't supported on Linux?  
  
If anyone is struggling with this, here is how I did it:  
- `git clone git@github.com:boostorg/beast.git`  
- `git checkout boost-1.66.0` (if you have different version, checkout that)  
- modify `CMakeLists.txt` by adding the following patch:  
```diff  
diff --git a/CMakeLists.txt b/CMakeLists.txt  
index 15580617..97ebdaae 100644  
--- a/CMakeLists.txt  
+++ b/CMakeLists.txt  
@@ -52,6 +52,10 @@ else()  
     set (THREADS_PREFER_PTHREAD_FLAG ON)  
     find_package (Threads)  
   
+    if (UNIX AND NOT APPLE)  
+      set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lpthread -lboost_system -lboost_filesystem -lboost_coroutine")  
+    endif()  
+  
     set( CMAKE_CXX_FLAGS  
       "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wpedantic -Wno-unused-parameter")  
```  
- `mkdir build && cd build && cmake .. && make -j`  
  
So the only problem was linking in my case.

---

## Comment 6

> Username: djarek  
> Created at: 2019-08-12 18:34:47 UTC  
> Url: https://github.com/boostorg/beast/issues/796#issuecomment-520543634  

@Osteri there's no particular reason, except the fact that barely anyone uses the cmake scripts to build beast's tests/examples.
