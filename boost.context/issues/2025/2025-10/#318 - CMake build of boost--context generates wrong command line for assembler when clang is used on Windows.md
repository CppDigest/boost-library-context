# #318 - CMake build of boost::context generates wrong command line for assembler when clang is used on Windows [Closed]

> Username: s13n  
> Created at: 2025-10-17 19:49:23 UTC  
> Updated at: 2025-10-20 20:20:16 UTC  
> Closed at: 2025-10-18 04:43:15 UTC  
> Url: https://github.com/boostorg/context/issues/318  

I integrated boost into my CMake project using FetchContent, and it builds fine when using the MSVC compiler, but when using the clang-cl 19.1.5 compiler I get the following error when assembler sources are compiled for boost::context:  
```  
  FAILED: _deps/boost-build/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_masm.asm.obj   
  C:\PROGRA~1\MICROS~2\2022\COMMUN~1\VC\Tools\MSVC\1444~1.352\bin\Hostx86\x64\ml64.exe -DBOOST_CONTEXT_DYN_LINK="" -DBOOST_CONTEXT_EXPORT=EXPORT -DBOOST_CONTEXT_NO_LIB="" -DBOOST_CONTEXT_SOURCE="" -DBOOST_ERROR_CODE_HEADER_ONLY -DBOOST_PROCESS_USE_STD_FS -DBOOST_SYSTEM_USE_UTF8 -Dboost_context_EXPORTS [...] /nologo /quiet -Wno-unused-command-line-argument -c -Fo _deps\boost-build\libs\context\CMakeFiles\boost_context.dir\src\asm\make_x86_64_ms_pe_masm.asm.obj [...]_deps\boost-src\libs\context\src\asm\make_x86_64_ms_pe_masm.asm  
  Microsoft (R) Macro Assembler (x64) Version 14.44.35217.0  
    
  Copyright (C) Microsoft Corporation.  All rights reserved.  
    
    
    
[...]build\x64-clang-Debug\MASM : fatal error A1013: invalid numerical command-line argument : /W  
```  
  
Apparently, command line options are mixed up between different tool versions, or perhaps the wrong assembler is being called.  
  
I used boost 1.88, but it happens with multiple recent versions. I am using Windows 11 on x86-64.  
  
The project compiles successfully with clang on Linux.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-10-17 23:11:42 UTC  
> Url: https://github.com/boostorg/context/issues/318#issuecomment-3417480967  

That's weird.  
  
The `-Wno-unused-command-line-argument` has been added by https://github.com/boostorg/context/pull/288, but a further PR, https://github.com/boostorg/context/pull/294, makes sure to apply it only when `BOOST_CONTEXT_ASSEMBLER` is not "masm" (which it should be under clang-cl).  
  
It's not clear why the logic doesn't work correctly in your case. What is `BOOST_CONTEXT_ASSEMBLER`?

---

## Comment 2

> Username: pdimov  
> Created at: 2025-10-17 23:17:35 UTC  
> Url: https://github.com/boostorg/context/issues/318#issuecomment-3417493926  

Ah, I see. #288 is in 1.88, but #294 is in 1.89. So this has been fixed in 1.89.

---

## Comment 3

> Username: s13n  
> Created at: 2025-10-20 20:20:16 UTC  
> Url: https://github.com/boostorg/context/issues/318#issuecomment-3423591716  

Confirming that it was solved in 1.89.0! My builds succeed with the latest boost version. Thanks!
