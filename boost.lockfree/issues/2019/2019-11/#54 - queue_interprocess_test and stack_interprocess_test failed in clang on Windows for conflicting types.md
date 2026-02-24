# #54 - queue_interprocess_test and stack_interprocess_test failed in clang on Windows for conflicting types [Closed]

> Username: yuxianch  
> Created at: 2019-11-21 08:17:01 UTC  
> Updated at: 2019-11-21 08:33:26 UTC  
> Closed at: 2019-11-21 08:27:30 UTC  
> Url: https://github.com/boostorg/lockfree/issues/54  

These two tests [queue_interprocess_test](https://github.com/boostorg/lockfree/tree/develop/test/queue_interprocess_test.cpp) and [stack_interprocess_test](https://github.com/boostorg/lockfree/tree/develop/test/stack_interprocess_test.cpp) failed in clang on Windows for conflicting types.  
Compilation command:  
```  
clang queue_interprocess_test.cpp -o queue_interprocess_test.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_USE_LIB=1 -DBOOST_THREAD_WIN32 -I"..\..\.."  
```  
Error message:  
```  
In file included from queue_interprocess_test.cpp:12:  
In file included from ../../..\boost/thread/thread.hpp:12:  
In file included from ../../..\boost/thread/thread_only.hpp:15:  
In file included from ../../..\boost/thread/win32/thread_data.hpp:11:  
In file included from ../../..\boost/thread/win32/thread_primitives.hpp:22:  
../../..\boost/winapi/semaphore.hpp:25:1: error: conflicting types for  
      'CreateSemaphoreA'  
CreateSemaphoreA(  
^  
../../..\boost/interprocess/detail/win32_api.hpp:926:51: note: previous  
      declaration is here  
extern "C" __declspec(dllimport) void * __stdcall CreateSemaphoreA(inter...  
                                                  ^  
```  
cl from msvc will successfully compile this test.  
```  
cl queue_interprocess_test.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /Zc:forScope /Zc:wchar_t /favor:blend /wd4675 /EHs /std:c++17   -D_CRT_USE_BUILTIN_OFFSETOF -c  -DBOOST_ALL_NO_LIB=1  -DBOOST_ATOMIC_STATIC_LINK=1  -DBOOST_CHRONO_STATIC_LINK=1  -DBOOST_TEST_NO_AUTO_LINK=1  -DBOOST_THREAD_BUILD_LIB=1  -DBOOST_THREAD_USE_LIB=1  -DBOOST_THREAD_WIN32  "-I..\..\.."  
```  
I am still investigating the reason. If you know why, please let me know. Thanks!

---

## Comment 1

> Username: yuxianch  
> Created at: 2019-11-21 08:27:30 UTC  
> Updated at: 2019-11-21 08:33:26 UTC  
> Url: https://github.com/boostorg/lockfree/issues/54#issuecomment-556974582  

Sorry. I found it duplicated with [boost thread issue 139](https://github.com/boostorg/thread/issues/139). Adding "-DBOOST_USE_WINDOWS_H" will fix this issue.
