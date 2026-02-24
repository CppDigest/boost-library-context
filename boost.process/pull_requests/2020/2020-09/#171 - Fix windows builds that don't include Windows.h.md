# #171 Fix windows builds that don't include Windows.h [Merged]

> Username: Shauren  
> Created at: 2020-09-03 11:23:36 UTC  
> Updated at: 2020-10-30 17:26:46 UTC  
> Merged at: 2020-10-29 17:17:05 UTC  
> Closed at: 2020-10-29 17:17:05 UTC  
> Url: https://github.com/boostorg/process/pull/171  

Removed __kernel_entry usage from Nt* function typedefs, is is part of SAL annoations, not neccessary for proper signature and calling convention  
  
Replaced global DWORD with ::boost::winapi::DWORD_  
  
Also should fix #96 (not verified, I use msvc, not mingw but error appears on the same line)

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2020-09-21 22:46:48 UTC  
> Url: https://github.com/boostorg/process/pull/171#issuecomment-696418849  

What version of MSVC are you using? I ran into issues with MSVC which is why I added the annotations.

---

## Comment 2

> Username: Shauren  
> Created_at: 2020-09-22 07:55:10 UTC  
> Url: https://github.com/boostorg/process/pull/171#issuecomment-696566817  

Microsoft Visual Studio Community 2019  
Version 16.7.3  
v142 toolset  
Microsoft (R) C/C++ Optimizing Compiler Version 19.27.29111 for x64

---

## Comment 3

> Username: Shauren  
> Created_at: 2020-09-22 13:56:18 UTC  
> Updated_at: 2020-09-22 13:58:33 UTC  
> Url: https://github.com/boostorg/process/pull/171#issuecomment-696738707  

I'll also add that __kernel_entry usage only causes issues with boost 1.73 and newer, because of this change in boost/winapi https://github.com/boostorg/winapi/commit/c523cc4b5d4e738b9491d0ce0130e7a75fc39d70  
  
Here is a part of /showIncludes trace with older version  
```  
Note: including file: C:/data/libraries/installed/x64-windows/include\boost/process/args.hpp  
...  
Note: including file:    C:/data/libraries/installed/x64-windows/include\boost/winapi/get_last_error.hpp  
Note: including file:     C:/data/libraries/installed/x64-windows/include\boost/winapi/basic_types.hpp  
...  
Note: including file:      C:/WinSdk/Include/10.0.18362.0/shared\winerror.h  
Note: including file:       C:/WinSdk/Include/10.0.18362.0/shared\specstrings.h  
Note: including file:        C:/WinSdk/Include/10.0.18362.0/shared\specstrings_strict.h  
Note: including file:         C:/WinSdk/Include/10.0.18362.0/shared\specstrings_undef.h  
```  
  
specstrings is where that define lives

---

## Comment 4

> Username: p13l13d13  
> Created_at: 2020-10-23 14:09:01 UTC  
> Url: https://github.com/boostorg/process/pull/171#issuecomment-715365713  

@klemens-morgenstern any updates on this? I can confirm that this PR fixes the issue on latest MSVC:  
  
`Microsoft (R) C/C++ Optimizing Compiler Version 19.27.29112 for x86`  
  
from Visual Studio 2019 16.7.

---
