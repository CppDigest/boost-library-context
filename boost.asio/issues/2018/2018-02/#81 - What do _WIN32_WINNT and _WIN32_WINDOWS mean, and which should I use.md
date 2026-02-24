# #81 - What do _WIN32_WINNT  and _WIN32_WINDOWS  mean, and which should I use? [Closed]

> Username: fake-name  
> Created at: 2018-02-08 02:38:54 UTC  
> Updated at: 2020-12-30 00:48:30 UTC  
> Closed at: 2020-12-30 00:48:12 UTC  
> Url: https://github.com/boostorg/asio/issues/81  

Basically the title. I'm working on a project which uses `boost.asio`, and every build outputs the following.  
  
```  
13>         Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
13>         - add -D_WIN32_WINNT=0x0501 to the compiler command line; or  
13>         - add _WIN32_WINNT=0x0501 to your project's Preprocessor Definitions.  
13>         Assuming _WIN32_WINNT=0x0501 (i.e. Windows XP target).  
13>         Generating Code...  
```  
  
Which tells me I need to do *something*, but not what either of the macros represent. Google is useless, mostly leading to people asking how to add preprocessor definitions.   
  
I'm on windows 7, which I *assume* should mean Windows NT-based kernel, but `_WIN32_WINNT=0x0501 (i.e. Windows XP target).` implies that this macro is for windows XP.   
  
Basically, does `_WIN32_WINNT=0x0501` mean XP **or later**? What does the magic `0x0501` mean?  
  
Maybe update the compiler warnings since XP has been End-of-Life for several years now, too?

---

## Comment 1

> Username: fake-name  
> Created at: 2018-02-08 02:42:28 UTC  
> Url: https://github.com/boostorg/asio/issues/81#issuecomment-363984520  

Ok, so the macros are actually *windows* feature flags (so googling for `boost _WIN32_WINNT` was counterproductive).  
  
Apparently I want `_WIN32_WINNT=0x0601`, which means windows 7 feature set, I think.  
  
Maybe put a link to https://msdn.microsoft.com/en-us/library/windows/desktop/aa383745(v=vs.85).aspx (or similar) in the error message? Or at least mention *what* the relevant macro is for.

---

## Comment 2

> Username: syedalamabbas  
> Created at: 2018-09-18 16:10:41 UTC  
> Url: https://github.com/boostorg/asio/issues/81#issuecomment-422454822  

You can find all the definitions [here](https://msdn.microsoft.com/en-us/library/6sehtctf.aspx). All you have to do is go to your project's properties, and add the preprocessor definition in C++ i.e. Config Properties >> C/C++ >> Preprocessor >> Preprocessor Definitions. For instance you would like it  to be targeted for Win 7 , then add this definition -   
  
> _WIN32_WINNT = 0x0601;

---

## Comment 3

> Username: feiyunw  
> Created at: 2020-02-21 13:30:05 UTC  
> Url: https://github.com/boostorg/asio/issues/81#issuecomment-589653375  

Fixed it by adding two files in the project:  
- targetver.h  
```  
#pragma once  
  
// // Including SDKDDKVer.h defines the highest available Windows platform.  
// If you wish to build your application for a previous Windows platform, include WinSDKVer.h and  
// set the _WIN32_WINNT macro to the platform you wish to support before including SDKDDKVer.h.  
#include <WinSDKVer.h>  
#define _WIN32_WINNT	0x0601  
#include <SDKDDKVer.h>  
```  
- framework.h  
```  
#pragma once  
  
#include "targetver.h"  
#define WIN32_LEAN_AND_MEAN             // Exclude rarely-used stuff from Windows headers  
// Windows Header Files  
#include <windows.h>  
// C RunTime Header Files  
#include <stdlib.h>  
#include <malloc.h>  
#include <memory.h>  
#include <tchar.h>  
```  
Include "framework.h" as the precompiled header file in the cpp file, before include <boost/asio.hpp>.  
Verified with Visual Studio 2017 (15.9.20). Hints from its Windows Desktop Application template.

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 00:47:53 UTC  
> Url: https://github.com/boostorg/asio/issues/81#issuecomment-752288844  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#596](https://github.com/chriskohlhoff/asio/issues/596).
