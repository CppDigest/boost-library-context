# #55 - __cxxabi_config.h missing in clang-6 ubuntu 16.04 [Closed]

> Username: djarek  
> Created at: 2019-04-22 01:29:29 UTC  
> Updated at: 2019-04-22 15:07:04 UTC  
> Closed at: 2019-04-22 08:18:32 UTC  
> Url: https://github.com/boostorg/core/issues/55  

https://dev.azure.com/damianjarek93/beast/_build/results?buildId=173&view=logs&jobId=e6838eee-2986-5b9a-5774-6c1eb3295bb2&taskId=1e9160d2-1b74-53b9-3c18-236685ff9951&lineStart=109&lineEnd=120&colStart=1&colEnd=29  
```  "/usr/bin/clang++" -c -x c++ -std=c++11 -fvisibility-inlines-hidden -stdlib=libc++ -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_SEPARATE_COMPILATION -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_BEAST_TESTS -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_COROUTINES_DYN_LINK=1 -DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -D_GNU_SOURCE=1 -D_XOPEN_SOURCE=600 -I"." -I"libs/beast" -I"libs/beast/test/extern" -I"libs/beast/test/extras/include" -o "bin.v2/libs/beast/test/doc/clang-linux-6.0.1/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/websocket_6_timeouts.o" "libs/beast/test/doc/websocket_6_timeouts.cpp"  
  
...failed clang-linux.compile.c++.without-pch bin.v2/libs/beast/test/doc/clang-linux-6.0.1/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/websocket_6_timeouts.o...  
clang-linux.compile.c++.without-pch bin.v2/libs/beast/test/doc/clang-linux-6.0.1/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/websocket_7_teardown.o  
In file included from libs/beast/test/doc/websocket_7_teardown.cpp:17:  
In file included from ./boost/beast.hpp:15:  
In file included from ./boost/beast/core.hpp:16:  
In file included from ./boost/beast/core/basic_stream.hpp:26:  
In file included from ./boost/enable_shared_from_this.hpp:16:  
In file included from ./boost/smart_ptr/enable_shared_from_this.hpp:16:  
In file included from ./boost/smart_ptr/weak_ptr.hpp:17:  
In file included from ./boost/smart_ptr/detail/shared_count.hpp:29:  
In file included from ./boost/smart_ptr/detail/sp_counted_base.hpp:45:  
In file included from ./boost/smart_ptr/detail/sp_counted_base_clang.hpp:18:  
In file included from ./boost/detail/sp_typeinfo.hpp:20:  
In file included from ./boost/core/typeinfo.hpp:135:  
In file included from ./boost/core/demangle.hpp:32:  
/usr/include/c++/v1/cxxabi.h:21:10: fatal error: '__cxxabi_config.h' file not found  
#include <__cxxabi_config.h>  
         ^~~~~~~~~~~~~~~~~~~  
1 error generated.  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2019-04-22 08:18:31 UTC  
> Updated at: 2019-04-22 08:21:22 UTC  
> Url: https://github.com/boostorg/core/issues/55#issuecomment-485360356  

That file is part of libc++abi-dev package, you need to have it installed.

---

## Comment 2

> Username: djarek  
> Created at: 2019-04-22 14:19:56 UTC  
> Url: https://github.com/boostorg/core/issues/55#issuecomment-485430702  

It is installed (note that the final error is from `/usr/include/c++/v1/cxxabi.h`).

---

## Comment 3

> Username: Lastique  
> Created at: 2019-04-22 15:00:25 UTC  
> Url: https://github.com/boostorg/core/issues/55#issuecomment-485442125  

I that case, libc++/libc++abi is broken on that system. There's nothing we can do about it.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-04-22 15:07:04 UTC  
> Url: https://github.com/boostorg/core/issues/55#issuecomment-485443955  

It's a bit of a problem because everything that includes `core/typeinfo.hpp`, even indirectly as in the above, breaks.  
  
Still not clear whether we can do anything about it, of course.
