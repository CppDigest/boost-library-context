# #52 - Windows target platform _WIN32_WINNT=0x0A00 (Win10) FAMILY=DESKTOP build failure [Closed]

> Username: jeking3  
> Created at: 2017-09-15 14:44:24 UTC  
> Updated at: 2017-09-16 16:30:00 UTC  
> Closed at: 2017-09-16 16:30:00 UTC  
> Url: https://github.com/boostorg/winapi/issues/52  

Build job: https://ci.appveyor.com/project/jeking3/winapi/build/job/9jtkes24qr42js6a  
```  
ompile-c-c++ bin.v2\libs\winapi\test\~hdr-post-winh-winapi-handles~hpp.test\msvc-14.1\release\threading-multi\~hdr-post-winh-winapi-handles~hpp.obj  
windows_h_post.cpp  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\handleapi.h(81): error C2732: linkage specification contradicts earlier specification for 'CompareObjectHandles'  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\handleapi.h(78): note: see declaration of 'CompareObjectHandles'  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2017-09-16 13:05:12 UTC  
> Url: https://github.com/boostorg/winapi/issues/52#issuecomment-329967352  

@Lastique Please set milestone to v1.66.0 for this issue.
