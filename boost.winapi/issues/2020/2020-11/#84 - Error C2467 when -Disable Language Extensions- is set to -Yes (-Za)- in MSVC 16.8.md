# #84 - Error C2467 when "Disable Language Extensions" is set to "Yes (/Za)" in MSVC 16.8 [Open]

> Username: mw4853  
> Created at: 2020-11-16 14:38:57 UTC  
> Updated at: 2023-02-18 08:00:05 UTC  
> Url: https://github.com/boostorg/winapi/issues/84  

Visual Studio 2019 v16.8 reports the following error on this line if you include <boost/chrono.hpp> in a Win32 project:  
  
boost-1.74\boost\winapi\basic_types.hpp(255,6): error C2467: illegal declaration of anonymous 'struct'  
  
https://github.com/boostorg/winapi/blob/37607cc3d3ea5145381f65b6fe51eac8bb8f361f/include/boost/winapi/basic_types.hpp#L255  
  
For this .vcxproj, I do have "Disable Language Extensions" set to "Yes (/Za)", which seems to be triggering this issue.  
Also: "C++ Language Standard" is set to "Default (ISO C++14 Standard)"  
"C Language Standard" is set to "Default (Legacy MSVC)"

---

## Comment 1

> Username: mw4853  
> Created at: 2020-11-16 14:58:24 UTC  
> Updated at: 2020-11-16 14:59:48 UTC  
> Url: https://github.com/boostorg/winapi/issues/84#issuecomment-728115441  

It looks like something like this is necessary (from winnt.h,  Windows 10 SDK 10.0.18362.0):  
  
```  
typedef union _LARGE_INTEGER {  
    struct {  
        DWORD LowPart;  
        LONG HighPart;  
    } DUMMYSTRUCTNAME;  
    struct {  
        DWORD LowPart;  
        LONG HighPart;  
    } u;  
    LONGLONG QuadPart;  
} LARGE_INTEGER;  
```  
  
Elsewhere in winnt.h:  
  
```  
#ifndef DUMMYSTRUCTNAME  
#if defined(NONAMELESSUNION) || !defined(_MSC_EXTENSIONS)  
#define DUMMYSTRUCTNAME  s  
#define DUMMYSTRUCTNAME2 s2  
#define DUMMYSTRUCTNAME3 s3  
#define DUMMYSTRUCTNAME4 s4  
#define DUMMYSTRUCTNAME5 s5  
#define DUMMYSTRUCTNAME6 s6  
#else  
#define DUMMYSTRUCTNAME  
#define DUMMYSTRUCTNAME2  
#define DUMMYSTRUCTNAME3  
#define DUMMYSTRUCTNAME4  
#define DUMMYSTRUCTNAME5  
#define DUMMYSTRUCTNAME6  
#endif  
#endif // DUMMYSTRUCTNAME  
```  
  
There is some information about DUMMYSTRUCTNAME here: https://devblogs.microsoft.com/oldnewthing/20170907-00/?p=96956

---

## Comment 2

> Username: Lastique  
> Created at: 2020-11-16 16:13:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/84#issuecomment-728162631  

I think it's too late to implement support for `/Za` in 1.75, so my suggestion for now is to compile with extensions enabled. I'll see what can be done past 1.75.

---

## Comment 3

> Username: walbourn  
> Created at: 2023-02-18 07:59:50 UTC  
> Updated at: 2023-02-18 08:00:05 UTC  
> Url: https://github.com/boostorg/winapi/issues/84#issuecomment-1435516663  

``/Za`` is deprecated in MSVC, and it reflects a *really old* ANSI version.  
  
The modern MSVC 'conformance' switches are:  
  
```  
/permissive- /Zc:__cplusplus /Zc:inline  
/Zc:preprocessor // 19.26 or later  
/Zc:lambda // 19.28 or later  
/Zc:templateScope // 19.35 or later  
```
