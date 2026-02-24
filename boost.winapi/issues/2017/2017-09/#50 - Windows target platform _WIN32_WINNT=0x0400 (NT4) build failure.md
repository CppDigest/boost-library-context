# #50 - Windows target platform _WIN32_WINNT=0x0400 (NT4) build failure [Closed]

> Username: jeking3  
> Created at: 2017-09-15 14:41:44 UTC  
> Updated at: 2017-09-16 16:29:41 UTC  
> Closed at: 2017-09-16 16:29:41 UTC  
> Url: https://github.com/boostorg/winapi/issues/50  

Build job: https://ci.appveyor.com/project/jeking3/winapi/build/job/ri0d2776mf80d1hn  
```  
ompile-c-c++ bin.v2\libs\winapi\test\~hdr-use-winh-winapi-jobs~hpp.test\msvc-14.1\debug\threading-multi\~hdr-use-winh-winapi-jobs~hpp.obj  
decl_header.cpp  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(80): error C2039: 'OpenJobObjectA': is not a member of '`global namespace''  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(80): error C2873: 'OpenJobObjectA': symbol cannot be used in a using-declaration  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(94): error C2039: 'CreateJobObjectA': is not a member of '`global namespace''  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(94): error C2664: 'boost::detail::winapi::HANDLE_ boost::detail::winapi::CreateJobObjectA(boost::detail::winapi::LPSECURITY_ATTRIBUTES_,boost::detail::winapi::LPCSTR_)': cannot convert argument 1 from '_SECURITY_ATTRIBUTES *' to 'boost::detail::winapi::LPSECURITY_ATTRIBUTES_'  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(94): note: Types pointed to are unrelated; conversion requires reinterpret_cast, C-style cast or function-style cast  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(99): error C2039: 'CreateJobObjectA': is not a member of '`global namespace''  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(99): error C2664: 'boost::detail::winapi::HANDLE_ boost::detail::winapi::CreateJobObjectA(boost::detail::winapi::LPSECURITY_ATTRIBUTES_,boost::detail::winapi::LPCSTR_)': cannot convert argument 1 from '_SECURITY_ATTRIBUTES *' to 'boost::detail::winapi::LPSECURITY_ATTRIBUTES_'  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(99): note: Types pointed to are unrelated; conversion requires reinterpret_cast, C-style cast or function-style cast  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(104): error C2039: 'OpenJobObjectA': is not a member of '`global namespace''  
C:\projects\boost-root\boost/detail/winapi/jobs.hpp(104): error C3861: 'OpenJobObjectA': identifier not found  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2017-09-16 13:04:57 UTC  
> Url: https://github.com/boostorg/winapi/issues/50#issuecomment-329967338  

@Lastique Please set milestone to v1.66.0 for this issue.

---

## Comment 2

> Username: Lastique  
> Created at: 2017-09-16 14:52:54 UTC  
> Url: https://github.com/boostorg/winapi/issues/50#issuecomment-329973242  

I don't set milestones.
