# #96 - boost v1.71 - Program fails to compile after including header : <boost/process.hpp> [Closed]

> Username: m7b  
> Created at: 2019-08-27 12:02:54 UTC  
> Updated at: 2020-10-29 17:17:05 UTC  
> Closed at: 2020-10-29 17:17:05 UTC  
> Url: https://github.com/boostorg/process/issues/96  

I dont't know if it is relatet to #92, but i can't compile the same source code with only the header included with boost 1.71.  
  
```  
In file included from W:/boost_1_71_0/boost/process/detail/windows/handles.hpp:11,  
                 from W:/boost_1_71_0/boost/process/detail/used_handles.hpp:17,  
                 from W:/boost_1_71_0/boost/process/detail/windows/async_in.hpp:20,  
                 from W:/boost_1_71_0/boost/process/async.hpp:49,  
                 from W:/boost_1_71_0/boost/process.hpp:23,  
                 from X:\trunk\test\test_boost_process.cpp:1:  
W:/boost_1_71_0/boost/process/detail/windows/handle_workaround.hpp:209:51: error: expected ')' before '*' token  
 typedef ::boost::winapi::NTSTATUS_ (__kernel_entry *nt_system_query_information_p )(  
                                    ~              ^~  
                                                   )  
W:/boost_1_71_0/boost/process/detail/windows/handle_workaround.hpp:224:51: error: expected ')' before '*' token  
 typedef ::boost::winapi::NTSTATUS_ (__kernel_entry *nt_query_object_p )(  
                                    ~              ^~  
                                                   )  
W:/boost_1_71_0/boost/process/detail/windows/handle_workaround.hpp: In function 'boost::winapi::NTSTATUS_ boost::process  
::detail::windows::workaround::nt_system_query_information(boost::process::detail::windows::workaround::SYSTEM_INFORMATI  
ON_CLASS_, void*, boost::winapi::ULONG_, boost::winapi::PULONG_)':  
W:/boost_1_71_0/boost/process/detail/windows/handle_workaround.hpp:240:12: error: 'nt_system_query_information_p' does n  
ot name a type; did you mean 'nt_system_query_information'?  
     static nt_system_query_information_p f = reinterpret_cast<nt_system_query_information_p>(::boost::winapi::get_proc_  
address(h, "NtQuerySystemInformation"));  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
            nt_system_query_information  
...

---

## Comment 1

> Username: sinkingsugar  
> Created at: 2019-10-16 03:05:32 UTC  
> Url: https://github.com/boostorg/process/issues/96#issuecomment-542489949  

Definitely a issue under MINGW, Small workaround for now: https://github.com/sinkingsugar/chainblocks/blob/5c6920d65daf22b169a3dfba9db63ef4f50814cd/src/core/blocks/process.cpp#L11

---

## Comment 2

> Username: gibbontrothy  
> Created at: 2020-04-02 22:22:19 UTC  
> Url: https://github.com/boostorg/process/issues/96#issuecomment-608122220  

It also works if you define:  
```  
-DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN  
```  
So I just added this to my `CMakeLists.txt` file:  
```cmake  
# Boost.Process 1.71+ fails without windows.h.  
# WIN32_LEAN_AND_MEAN avoids issues with asio.  
# https://github.com/boostorg/process/issues/96  
if(Boost_VERSION GREATER 107000)  
  add_definitions(-DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN)  
endif()  
```

---

## Comment 3

> Username: Gei0r  
> Created at: 2020-04-25 20:10:23 UTC  
> Updated at: 2020-04-25 20:10:39 UTC  
> Url: https://github.com/boostorg/process/issues/96#issuecomment-619434142  

> It also works if you define:  
>   
> ```  
> -DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN  
> ```  
  
I also had to `#include <windows.h>` myself.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2020-04-27 12:15:27 UTC  
> Url: https://github.com/boostorg/process/issues/96#issuecomment-619948018  

Are all you guys using mingw? And does mingw work with boost.dll?

---

## Comment 5

> Username: Gei0r  
> Created at: 2020-05-05 09:25:51 UTC  
> Updated at: 2020-05-05 09:26:18 UTC  
> Url: https://github.com/boostorg/process/issues/96#issuecomment-623950573  

Yeah, I'm using mingw64 installed through MSYS2.  
Haven't tried boost.dll.

---

## Comment 6

> Username: gibbontrothy  
> Created at: 2020-06-01 21:52:47 UTC  
> Url: https://github.com/boostorg/process/issues/96#issuecomment-637133931  

I'm also using msys2/mingw64.  I'm not sure what boost.dll means for msys2, but I do use static and dynamic linking.
