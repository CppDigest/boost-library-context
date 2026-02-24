# #224 - Compilation fails with '/permissive-' in Boost 1.75.0 when "boost/process/group.hpp" is included [Open]

> Username: vaishnavkatiyar  
> Created at: 2021-10-12 06:49:39 UTC  
> Updated at: 2022-01-14 13:39:19 UTC  
> Url: https://github.com/boostorg/process/issues/224  

The following error is generated when the command `cl /I.\.. /EHsc /permissive- Source.cpp` is used:  
```  
Microsoft (R) C/C++ Optimizing Compiler Version 19.27.29112 for x86  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
Source.cpp  
.\..\boost/process/detail/windows/handles.hpp(146): error C2039: 'GetHandleInformation': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(146): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_setup(Executor &) const'  
.\..\boost/process/detail/windows/handles.hpp(147): error C2039: 'HANDLE_FLAG_INHERIT_': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(147): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_setup(Executor &) const'  
.\..\boost/process/detail/windows/handles.hpp(158): error C2039: 'SetHandleInformation': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(158): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_setup(Executor &) const'  
.\..\boost/process/detail/windows/handles.hpp(158): error C2039: 'HANDLE_FLAG_INHERIT_': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(158): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_setup(Executor &) const'  
.\..\boost/process/detail/windows/handles.hpp(165): error C2039: 'SetHandleInformation': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(165): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_error(Executor &,const std::error_code &) const'  
.\..\boost/process/detail/windows/handles.hpp(165): error C2039: 'HANDLE_FLAG_INHERIT_': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(165): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_error(Executor &,const std::error_code &) const'  
.\..\boost/process/detail/windows/handles.hpp(165): error C2039: 'HANDLE_FLAG_INHERIT_': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(165): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_error(Executor &,const std::error_code &) const'  
.\..\boost/process/detail/windows/handles.hpp(172): error C2039: 'SetHandleInformation': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(172): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_sucess(Executor &) const'  
.\..\boost/process/detail/windows/handles.hpp(172): error C2039: 'HANDLE_FLAG_INHERIT_': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(172): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_sucess(Executor &) const'  
.\..\boost/process/detail/windows/handles.hpp(172): error C2039: 'HANDLE_FLAG_INHERIT_': is not a member of 'boost::winapi'  
.\..\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
.\..\boost/process/detail/windows/handles.hpp(172): note: This diagnostic occurred in the compiler generated function 'void boost::process::detail::windows::limit_handles_::on_sucess(Executor &) const'  
```  
Source.cpp:  
```  
#define _WIN32_WINDOWS  
#include <boost/process/group.hpp>  
  
int main() {  
    getchar();  
    return 0;  
}  
```  
Including the header `boost/winapi/handle_info.hpp` in `boost/process/detail/windows/handles.hpp` resolves the above issue.

---

## Comment 1

> Username: Tradias  
> Created at: 2022-01-14 13:39:19 UTC  
> Url: https://github.com/boostorg/process/issues/224#issuecomment-1013126127  

Seems like a duplicate of https://github.com/boostorg/process/issues/183
