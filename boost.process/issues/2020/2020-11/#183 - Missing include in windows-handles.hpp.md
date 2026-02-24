# #183 - Missing include in windows/handles.hpp [Closed]

> Username: Tradias  
> Created at: 2020-11-11 22:02:20 UTC  
> Updated at: 2024-04-15 15:11:47 UTC  
> Closed at: 2024-04-15 15:11:47 UTC  
> Url: https://github.com/boostorg/process/issues/183  

Compiling code that uses `boost::process::child` with a custom `on_setup` handler with MSVC 16.8 throws the following errors (boost 1.74):  
  
```  
[build] boost/process/detail/windows/handles.hpp(142): error C2039: "GetHandleInformation" ist kein Member von "boost::winapi".  
[build] boost/process/detail/windows/handles.hpp(142): note: Diese Diagnose trat in der vom Compiler generierten Funktion "void boost::process::detail::windows::limit_handles_::on_setup(Executor &) const" auf.  
[build] boost/process/detail/windows/handles.hpp(143): error C2039: "HANDLE_FLAG_INHERIT_" ist kein Member von "boost::winapi".  
[build] boost/winapi/dll.hpp(190): note: Siehe Deklaration von "boost::winapi"  
[build] boost/process/detail/windows/handles.hpp(143): note: Diese Diagnose trat in der vom Compiler generierten Funktion "void boost::process::detail::windows::limit_handles_::on_setup(Executor &) const" auf.  
[build] boost/process/detail/windows/handles.hpp(154): error C2039: "SetHandleInformation" ist kein Member von "boost::winapi".  
[build] boost/winapi/dll.hpp(190): note: Siehe Deklaration von "boost::winapi"  
```  
  
The missing functions are declared in `#include <boost/winapi/handle_info.hpp>`. Adding that include to `boost/process/detail/windows/handles.hpp` solves the problem.

---

## Comment 1

> Username: Tradias  
> Created at: 2021-08-19 13:58:35 UTC  
> Url: https://github.com/boostorg/process/issues/183#issuecomment-901939135  

Remains to be an issue in Boost 1.77 and MSVC 16.11

---

## Comment 2

> Username: oxygene  
> Created at: 2022-02-17 10:50:50 UTC  
> Updated at: 2022-02-17 10:51:15 UTC  
> Url: https://github.com/boostorg/process/issues/183#issuecomment-1042817519  

Still a problem with Visual Studio 2022, v19.30 and boost 1.78.0.  
  
```  
C:\dev\boost-1.78.0\boost-1_78\boost/process/detail/windows/handles.hpp(141): error C2039: 'INVALID_HANDLE_VALUE_': is not a member of 'boost::winapi'  
C:\dev\boost-1.78.0\boost-1_78\boost/winapi/get_current_process_id.hpp(26): note: see declaration of 'boost::winapi'  
C:\dev\boost-1.78.0\boost-1_78\boost/process/detail/windows/handles.hpp(132): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_setup(Executor &) const'  
C:\dev\boost-1.78.0\boost-1_78\boost/process/detail/windows/handles.hpp(142): error C2039: 'GetHandleInformation': is not a member of 'boost::winapi'  
```  
  
Removing `/permissive-` or overriding with `/permissive` helps, but feels like a hack for me as `permissive-` is the default for all my portable builds.  
  
Whoever finds this bug report and uses CMake, this should help:  
```  
if (MSVC)  
    # See https://github.com/boostorg/process/issues/183  
    target_compile_options(MyTargetName PRIVATE "/permissive")  
endif ()  
```

---

## Comment 3

> Username: Tradias  
> Created at: 2024-04-15 15:11:47 UTC  
> Url: https://github.com/boostorg/process/issues/183#issuecomment-2057099473  

Seems to be resolved in Boost 1.84 and MSVC 17.9.
