# #139 - Clang on Windows build failures due to duplicated Windows API definitions [Closed]

> Username: SSE4  
> Created at: 2017-08-31 07:55:32 UTC  
> Updated at: 2018-03-09 20:28:44 UTC  
> Closed at: 2017-08-31 16:42:05 UTC  
> Url: https://github.com/boostorg/thread/issues/139  

boost thread doesn't compile with Clang 4.0.1 on Windows, there are a lot of errors about duplicated Windows API definitions  
```  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
.\boost/thread/win32/thread_primitives.hpp:173:55: error: conflicting types for 'CreateMutexA'  
                __declspec(dllimport) void* __stdcall CreateMutexA(_SECURITY_ATTRIBUTES*,int,char const*);  
                                                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\synchapi.h:489:1: note: previous declaration is here  
CreateMutexA(  
^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
.\boost/thread/win32/thread_primitives.hpp:174:55: error: conflicting types for 'CreateSemaphoreA'  
                __declspec(dllimport) void* __stdcall CreateSemaphoreA(_SECURITY_ATTRIBUTES*,long,long,char const*);  
                                                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\winbase.h:3009:1: note: previous declaration is here  
CreateSemaphoreA(  
^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
.\boost/thread/win32/thread_primitives.hpp:175:55: error: conflicting types for 'CreateEventA'  
                __declspec(dllimport) void* __stdcall CreateEventA(_SECURITY_ATTRIBUTES*,int,int,char const*);  
                                                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\synchapi.h:530:1: note: previous declaration is here  
CreateEventA(  
^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
.\boost/thread/win32/thread_primitives.hpp:177:55: error: functions that differ only in their return type cannot be overloaded  
                __declspec(dllimport) void* __stdcall GetModuleHandleA(char const*);  
                                                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\libloaderapi.h:300:1: note: previous declaration is here  
GetModuleHandleA(  
^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
.\boost/thread/win32/thread_primitives.hpp:194:54: warning: redeclaration of 'Sleep' should not add 'dllimport' attribute [-Wdll-attribute-on-redeclaration]  
                __declspec(dllimport) void __stdcall Sleep(unsigned long);  
                                                     ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\synchapi.h:820:1: note: previous declaration is here  
Sleep(  
^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
.\boost/thread/win32/thread_primitives.hpp:197:59: error: functions that differ only in their return type cannot be overloaded  
                __declspec(dllimport) farproc_t __stdcall GetProcAddress(void *, const char *);  
                                                          ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\libloaderapi.h:384:1: note: previous declaration is here  
GetProcAddress(  
^  
```  
my user config  
```  
using clang : 5.0 :  
 "C:/Program Files/LLVM/bin/clang.exe" :  
 <compileflags>-fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0501  
 <ranlib>"C:/Program Files/LLVM/bin/llvm-ranlib.exe"   
 <archiver>"C:/Program Files/LLVM/bin/llvm-ar.exe"   
 <linkflags>-fuse-ld=lld ;  
```

---

## Comment 1

> Username: MarcelRaad  
> Created at: 2017-08-31 08:40:15 UTC  
> Url: https://github.com/boostorg/thread/issues/139#issuecomment-326230121  

You need to compile with `BOOST_USE_WINDOWS_H` defined.

---

## Comment 2

> Username: SSE4  
> Created at: 2017-08-31 10:00:29 UTC  
> Url: https://github.com/boostorg/thread/issues/139#issuecomment-326249687  

@MarcelRaad  yes, that helped, thanks a lot

---

## Comment 3

> Username: egorpugin  
> Created at: 2018-03-09 15:09:11 UTC  
> Url: https://github.com/boostorg/thread/issues/139#issuecomment-371839298  

Is this issue fixed? I still see errors with 1.66.0.  
Maybe it's possible automatically turn on BOOST_USE_WINDOWS_H when using clang on win? In boost/thread/config or boost/config?

---

## Comment 4

> Username: viboes  
> Created at: 2018-03-09 20:28:44 UTC  
> Url: https://github.com/boostorg/thread/issues/139#issuecomment-371936431  

Which file is including `libloaderapi.h`?  
Is this file included by default?  
If yes, I could accepts a PR that defines `BOOST_USE_WINDOWS_H`, but I believe that it will be better to provide it for `boost/config.h.`
