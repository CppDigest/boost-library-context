# #107 - Boost 1.81.0 (chrono and thread) library compilation is failing with Sun Studio 12.5 [Open]

> Username: thriveni-susheela  
> Created at: 2023-03-03 02:11:10 UTC  
> Updated at: 2023-03-03 05:10:17 UTC  
> Url: https://github.com/boostorg/system/issues/107  



---

## Comment 1

> Username: thriveni-susheela  
> Created at: 2023-03-03 02:14:47 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452848728  

Facing below error when i am compiling chrono or thread alone in sun studio 12.5 Error I am getting while build boost thread/ chrono is below  
  
"CC" -std=c++11 -m64 -xarch=sparc -mt  -lCstd -xO4 -mt -erroff=%none -xldscope=hidden -m64 -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX -DNDEBUG -I"." -c -o "bin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden/future.o" "libs/thread/src/future.cpp"  
...failed sun.compile.c++ bin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden/future.o...  
...skipped <pbin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread.a for lack of <pbin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden>pthread/thread.o...  
...skipped <p/export/home/ramesh/compiled_library/boost_1_81_0/lib>libboost_thread.a for lack of <pbin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread.a...  
...skipped <pbin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread-variant-static.cmake for lack of <pbin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread.a...  
...skipped <p/export/home/ramesh/compiled_library/boost_1_81_0/lib/cmake/boost_thread-1.81.0>libboost_thread-variant-static.cmake for lack of <pbin.v2/libs/thread/build/sun/release/address-model-64/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread-variant-static.cmake...  
sun.compile.c++ bin.v2/libs/thread/build/sun/release/address-model-64/threadapi-pthread/threading-multi/visibility-hidden/pthread/thread.o  
"./boost/system/detail/error_condition.hpp", line 298: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 272: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 310: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 284: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 298: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 272: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 298: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 272: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 310: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 284: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 310: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_condition.hpp", line 284: Error: type is not a member of boost::system::detail::enable_if<0, void>.  
"./boost/system/detail/error_code.hpp", line 173: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/detail/error_code.hpp", line 190: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/detail/error_code.hpp", line 195: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/detail/error_code.hpp", line 200: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/detail/std_category_impl.hpp", line 62: Error: Too many initializers for bc.  
"./boost/system/detail/std_category_impl.hpp", line 67: Error: Too many initializers for bc.  
"./boost/system/detail/std_category_impl.hpp", line 75: Error: Too many initializers for bc.  
"./boost/system/errc.hpp", line 36: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/errc.hpp", line 42: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/system_error.hpp", line 38: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/system_error.hpp", line 38: Error: Too many initializers for code_.  
"./boost/system/system_error.hpp", line 41: Error: Too many arguments in cast to boost::system::error_code.  
"./boost/system/system_error.hpp", line 41: Error: Too many initializers for code_.  
Compilation aborted, too many Error messages.  
[1:40 PM]  
Using Same compiler option (C++11) all other libraries in boost able to build. These two Chrono and thread is giving similar to above error. Above error pasted is from boost thread compilation.  
  
**Command which have used to build:**  
./b2 install address-model=64 runtime-link=shared "cflags=-std=c++11 linkflags=-std=c++11" threading=multi toolset=sun --prefix=/export/home/ramesh/compiled_library/boost_1_81_0/ --with-chrono  
  
**Environment**  
Sun OS:5.10 Sparc  
Compiler: Sun studio 12.5  
  
**Command which have used to build:** ./b2 install address-model=64 runtime-link=shared "cflags=-std=c++11 linkflags=-std=c++11" threading=multi toolset=sun --prefix=/export/home/ramesh/compiled_library/boost_1_81_0/ --with-chrono

---

## Comment 2

> Username: pdimov  
> Created at: 2023-03-03 02:28:32 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452862403  

This looks like a compiler bug. Can you upgrade to Sun Studio 12.6? If not, or if 12.6 is also unable to compile the code, you'll need to use an earlier release of Boost.

---

## Comment 3

> Username: thriveni-susheela  
> Created at: 2023-03-03 03:21:47 UTC  
> Updated at: 2023-03-03 03:22:25 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452901145  

Hi @pdimov , thanks for the info. We can not upgrade to Sun Studio 12.6  and we are facing same issue with 1.80.0 and older.  So, my ask here is, can't we build/resolve the issue which am facing with sun studio 12.5 compiler .

---

## Comment 4

> Username: pdimov  
> Created at: 2023-03-03 03:28:54 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452908578  

Looking at the list of errors, the first 20 or so of them all refer to code that has been introduced in 1.81. What are the errors you are getting with Boost 1.80?

---

## Comment 5

> Username: thriveni-susheela  
> Created at: 2023-03-03 04:21:38 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452945479  

Hi @pdimov , thanks for quick reply. we are facing below error  while building thread  but we are able to build chrono with 1.80.0  
"/opt/SUNSTUDIO_12.5/developerstudio12.5/lib/compilers/CC-gcc/include/c++/5.1.0/cxxabi.h", line 595: Error: Only one of a set of overloaded functions can be extern "C".

---

## Comment 6

> Username: pdimov  
> Created at: 2023-03-03 04:39:41 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452958222  

This error comes from a header supplied with the compiler - to which I don't have access - and may be unrelated to any Boost code. Does Oracle provide support for Sun Studio 12.5? If so, they may have something to say about it.

---

## Comment 7

> Username: thriveni-susheela  
> Created at: 2023-03-03 05:02:03 UTC  
> Updated at: 2023-03-03 05:06:45 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452976916  

Hi @pdimov , thanks a lot.  Will be there any fix for 1.81.0 issues we are encountering in future ??

---

## Comment 8

> Username: pdimov  
> Created at: 2023-03-03 05:10:16 UTC  
> Url: https://github.com/boostorg/system/issues/107#issuecomment-1452983672  

Unlikely. 12.5 is quite old, and we don't test Boost with any Sun compiler. I sometimes incorporate Sun-specific fixes submitted as pull requests such as this one https://github.com/boostorg/system/pull/66 by @vahtis, but I don't have access to the compiler myself.
