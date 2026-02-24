# #41 - switch to boost winapi to avoid duplicated definitions when compili… [Closed]

> Username: SSE4  
> Created at: 2017-09-01 07:22:37 UTC  
> Updated at: 2017-09-01 13:29:28 UTC  
> Closed at: 2017-09-01 13:29:28 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/41  

…ng with Clang on Windows

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-09-01 08:54:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/41#issuecomment-326527665  

What problem does this fix?

---

## Comment 2

> Username: SSE4  
> Created_at: 2017-09-01 09:07:31 UTC  
> Updated_at: 2017-09-01 09:08:30 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/41#issuecomment-326530587  

@pdimov   
if compile on Windows with Clang it triggers lots of errors:  
```  
clang-linux.compile.c++.without-pth bin.v2\libs\math\build\clang-linux-5.0\release\link-static\pch-off\threading-multi\assoc_legendre.obj  
  
  "C:/Program Files/LLVM/bin/clang.exe" -c -x c++ -O3 -Wno-inline -Wall -fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0502 -DBOOST_USE_WINDOWS_H=1 -Wno-c++11-narrowing -D_WIN32_WINNT=0x0502 -DNOMINMAX -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DNDEBUG -I"." -I"libs\math\src\tr1" -o "bin.v2\libs\math\build\clang-linux-5.0\release\link-static\pch-off\threading-multi\assoc_legendre.obj" "libs\math\build\..\src\tr1\assoc_legendre.cpp"  
  
In file included from libs\math\build\..\src\tr1\assoc_legendre.cpp:11:  
In file included from .\boost/math/special_functions/legendre.hpp:17:  
In file included from .\boost/math/special_functions/factorials.hpp:14:  
In file included from .\boost/math/special_functions/gamma.hpp:35:  
In file included from .\boost/math/special_functions/bernoulli.hpp:16:  
In file included from .\boost/math/special_functions/detail/bernoulli_details.hpp:11:  
In file included from .\boost/detail/lightweight_mutex.hpp:20:  
In file included from .\boost/smart_ptr/detail/lightweight_mutex.hpp:36:  
.\boost/smart_ptr/detail/lwm_win32_cs.hpp:86:9: error: no member named 'InitializeCriticalSection' in namespace 'boost::detail'; did you mean simply 'InitializeCriticalSection'?  
        boost::detail::InitializeCriticalSection(reinterpret_cast< ::_RTL_CRITICAL_SECTION* >(&cs_));  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
        InitializeCriticalSection  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\synchapi.h:152:1: note: 'InitializeCriticalSection' declared here  
InitializeCriticalSection(  
^  
In file included from libs\math\build\..\src\tr1\assoc_legendre.cpp:11:  
In file included from .\boost/math/special_functions/legendre.hpp:17:  
In file included from .\boost/math/special_functions/factorials.hpp:14:  
In file included from .\boost/math/special_functions/gamma.hpp:35:  
In file included from .\boost/math/special_functions/bernoulli.hpp:16:  
In file included from .\boost/math/special_functions/detail/bernoulli_details.hpp:11:  
In file included from .\boost/detail/lightweight_mutex.hpp:20:  
In file included from .\boost/smart_ptr/detail/lightweight_mutex.hpp:36:  
.\boost/smart_ptr/detail/lwm_win32_cs.hpp:92:9: error: no member named 'DeleteCriticalSection' in namespace 'boost::detail'; did you mean simply 'DeleteCriticalSection'?  
        boost::detail::DeleteCriticalSection(reinterpret_cast< ::_RTL_CRITICAL_SECTION* >(&cs_));  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
        DeleteCriticalSection  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\synchapi.h:235:1: note: 'DeleteCriticalSection' declared here  
DeleteCriticalSection(  
^  
In file included from libs\math\build\..\src\tr1\assoc_legendre.cpp:11:  
In file included from .\boost/math/special_functions/legendre.hpp:17:  
In file included from .\boost/math/special_functions/factorials.hpp:14:  
In file included from .\boost/math/special_functions/gamma.hpp:35:  
In file included from .\boost/math/special_functions/bernoulli.hpp:16:  
In file included from .\boost/math/special_functions/detail/bernoulli_details.hpp:11:  
In file included from .\boost/detail/lightweight_mutex.hpp:20:  
In file included from .\boost/smart_ptr/detail/lightweight_mutex.hpp:36:  
.\boost/smart_ptr/detail/lwm_win32_cs.hpp:111:13: error: no member named 'EnterCriticalSection' in namespace 'boost::detail'; did you mean simply 'EnterCriticalSection'?  
            boost::detail::EnterCriticalSection(reinterpret_cast< ::_RTL_CRITICAL_SECTION* >(&m_.cs_));  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
            EnterCriticalSection  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\synchapi.h:172:1: note: 'EnterCriticalSection' declared here  
EnterCriticalSection(  
^  
In file included from libs\math\build\..\src\tr1\assoc_legendre.cpp:11:  
In file included from .\boost/math/special_functions/legendre.hpp:17:  
In file included from .\boost/math/special_functions/factorials.hpp:14:  
In file included from .\boost/math/special_functions/gamma.hpp:35:  
In file included from .\boost/math/special_functions/bernoulli.hpp:16:  
In file included from .\boost/math/special_functions/detail/bernoulli_details.hpp:11:  
In file included from .\boost/detail/lightweight_mutex.hpp:20:  
In file included from .\boost/smart_ptr/detail/lightweight_mutex.hpp:36:  
.\boost/smart_ptr/detail/lwm_win32_cs.hpp:116:13: error: no member named 'LeaveCriticalSection' in namespace 'boost::detail'; did you mean simply 'LeaveCriticalSection'?  
            boost::detail::LeaveCriticalSection(reinterpret_cast< ::_RTL_CRITICAL_SECTION* >(&m_.cs_));  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
            LeaveCriticalSection  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\synchapi.h:180:1: note: 'LeaveCriticalSection' declared here  
LeaveCriticalSection(  
^  
4 errors generated.  
```  
my user config:  
```  
using clang : 5.0 :  
 "C:/Program Files/LLVM/bin/clang.exe" :  
 <compileflags>"-fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0502 -DBOOST_USE_WINDOWS_H=1 -Wno-c++11-narrowing -D_WIN32_WINNT=0x0502 -DNOMINMAX"  
 <ranlib>"C:/Program Files/LLVM/bin/llvm-ranlib.exe"   
 <archiver>"C:/Program Files/LLVM/bin/llvm-ar.exe"   
 <linkflags>-fuse-ld=lld ;  
```

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-09-01 09:45:29 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/41#issuecomment-326538699  

Looks like I broke `BOOST_USE_WINDOWS_H` (for all compilers, not just Clang) in https://github.com/boostorg/smart_ptr/commit/7ef8fa4a19bac256ec3c8d9588d49f5df5a74269  
  
Will fix.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-09-01 10:26:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/41#issuecomment-326546933  

Here: https://github.com/boostorg/smart_ptr/commit/868a870a596b3f1fb49f96a09d34ba3765c83eee

---
