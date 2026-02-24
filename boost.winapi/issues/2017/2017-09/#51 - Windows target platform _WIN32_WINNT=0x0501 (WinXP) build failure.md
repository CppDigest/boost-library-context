# #51 - Windows target platform _WIN32_WINNT=0x0501 (WinXP) build failure [Closed]

> Username: jeking3  
> Created at: 2017-09-15 14:43:03 UTC  
> Updated at: 2017-09-16 16:29:51 UTC  
> Closed at: 2017-09-16 16:29:51 UTC  
> Url: https://github.com/boostorg/winapi/issues/51  

Build job: https://ci.appveyor.com/project/jeking3/winapi/build/job/s2mbgr4viurq20q9  
```  
compile-c-c++ bin.v2\libs\winapi\test\~hdr-use-winh-winapi-stack_backtrace~hpp.test\msvc-14.1\release\threading-multi\~hdr-use-winh-winapi-stack_backtrace~hpp.obj  
decl_header.cpp  
.\boost/detail/winapi/stack_backtrace.hpp(45): error C2039: 'RtlCaptureStackBackTrace': is not a member of '`global namespace''  
.\boost/detail/winapi/stack_backtrace.hpp(45): error C2873: 'RtlCaptureStackBackTrace': symbol cannot be used in a using-declaration  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2017-09-16 13:05:06 UTC  
> Url: https://github.com/boostorg/winapi/issues/51#issuecomment-329967348  

@Lastique Please set milestone to v1.66.0 for this issue.
