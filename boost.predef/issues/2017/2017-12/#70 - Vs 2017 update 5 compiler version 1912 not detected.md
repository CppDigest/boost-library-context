# #70 - Vs 2017 update 5 compiler version 1912 not detected [Closed]

> Username: elnono  
> Created at: 2017-12-13 08:28:40 UTC  
> Updated at: 2018-01-23 13:34:51 UTC  
> Closed at: 2018-01-23 13:34:51 UTC  
> Url: https://github.com/boostorg/predef/issues/70  

Boost is not able to detecte _MSC_VER 1912 and _MSC_FULL_VER 191225830.  
  
The issue is in visualc.h  
 _MSC_FULL_VER is defined so the code below is not able to parse the version.  
so it triggers an error.  
```  
#define BOOST_COMP_MSVC BOOST_VERSION_NUMBER_NOT_AVAILABLE  
  
#if defined(_MSC_VER)  
#   if !defined (_MSC_FULL_VER)  
#       define BOOST_COMP_MSVC_BUILD 0  
#   else  
        /* how many digits does the build number have? */  
#       if _MSC_FULL_VER / 10000 == _MSC_VER  
            /* four digits */  
#           define BOOST_COMP_MSVC_BUILD (_MSC_FULL_VER % 10000)  
#       elif _MSC_FULL_VER / 100000 == _MSC_VER  
            /* five digits */  
#           define BOOST_COMP_MSVC_BUILD (_MSC_FULL_VER % 100000)  
#       else  
#           error "Cannot determine build number from _MSC_FULL_VER"  
#       endif  
#   endif  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-01-23 13:29:18 UTC  
> Url: https://github.com/boostorg/predef/issues/70#issuecomment-359790382  

@grafikrobot I am not able to reproduce this issue:  
```  
**********************************************************************  
** Visual Studio 2017 Developer Command Prompt v15.5.4  
** Copyright (c) 2017 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x64'  
  
C:\Users\jim.king\Source>cl /?  
Microsoft (R) C/C++ Optimizing Compiler Version 19.12.25834 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
```  
  
Result from info_as_cpp.exe:  
```  
c:\boost\libs\predef\test>..\..\..\b2 toolset=msvc-14.1 address-model=64 info_as_cpp  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
...found 343 targets...  
...updating 8 targets...  
compile-c-c++ ..\..\..\bin.v2\libs\predef\test\info_as_cpp.test\msvc-14.1\debug\address-model-64\threadapi-win32\threading-multi\info_as_cpp.obj  
info_as_cpp.cpp  
msvc.link ..\..\..\bin.v2\libs\predef\test\info_as_cpp.test\msvc-14.1\debug\address-model-64\threadapi-win32\threading-multi\info_as_cpp.exe  
msvc.manifest ..\..\..\bin.v2\libs\predef\test\info_as_cpp.test\msvc-14.1\debug\address-model-64\threadapi-win32\threading-multi\info_as_cpp.exe  
testing.capture-output ..\..\..\bin.v2\libs\predef\test\info_as_cpp.test\msvc-14.1\debug\address-model-64\threadapi-win32\threading-multi\info_as_cpp.run  
        1 file(s) copied.  
**passed** ..\..\..\bin.v2\libs\predef\test\info_as_cpp.test\msvc-14.1\debug\address-model-64\threadapi-win32\threading-multi\info_as_cpp.test  
...updated 8 targets...  
  
c:\boost\libs\predef\test>..\..\..\bin.v2\libs\predef\test\info_as_cpp.test\msvc-14.1\debug\address-model-64\threadapi-win32\threading-multi\info_as_cpp.exe  
** Detected **  
BOOST_ARCH_X86 = 1 (0,0,1) | Intel x86  
BOOST_ARCH_X86_64 = 1 (0,0,1) | Intel x86-64  
BOOST_COMP_MSVC = 191225834 (19,12,25834) | Microsoft Visual C/C++  
BOOST_ENDIAN_LITTLE_BYTE = 1 (0,0,1) | Byte-Swapped Little-Endian  
BOOST_HW_SIMD = 20000000 (2,0,0) | Hardware SIMD  
BOOST_HW_SIMD_X86 = 20000000 (2,0,0) | x86 SIMD  
BOOST_LANG_STDCPP = 271100001 (27,11,1) | Standard C++  
BOOST_LIB_STD_DINKUMWARE = 65000000 (6,50,0) | Dinkumware  
BOOST_OS_WINDOWS = 1 (0,0,1) | Microsoft Windows  
BOOST_PLAT_WINDOWS_DESKTOP = 1 (0,0,1) | Windows Desktop  
BOOST_PLAT_WINDOWS_UWP = 1 (0,0,1) | Universal Windows Platform  
** Not Detected **  
BOOST_ARCH_ALPHA = 0 | DEC Alpha  
BOOST_ARCH_ARM = 0 | ARM  
...  
```  
  
This can be closed.
