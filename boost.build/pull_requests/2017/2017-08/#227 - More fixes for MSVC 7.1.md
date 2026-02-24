# #227 More fixes for MSVC 7.1 [Merged]

> Username: igaztanaga  
> Created at: 2017-08-13 23:15:14 UTC  
> Updated at: 2021-10-02 22:08:38 UTC  
> Merged at: 2017-08-14 03:24:58 UTC  
> Closed at: 2017-08-14 03:24:58 UTC  
> Url: https://github.com/boostorg/build/pull/227  

- Add missing WINVER=0x0501 and _WIN32_WINNT=0x0501 to config_toolset.bat  
- Add needed -D_WIN32_WINNT=0x0501 for vc7 to fix unresolved symbols  
- Added alternatives to va_copy and vsnprintf in debugger.c  
- Add missing fix from previous patch in strings.c

---
