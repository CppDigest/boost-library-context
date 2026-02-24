# #313 - [build] masm: clang-cl fails to compile [Closed]

> Username: crueter  
> Created at: 2025-08-29 01:53:37 UTC  
> Updated at: 2025-09-08 01:44:19 UTC  
> Closed at: 2025-09-08 01:44:19 UTC  
> Url: https://github.com/boostorg/context/issues/313  

This error is repeated for each asm file:  
  
```  
FAILED: _deps/boost-build/libs/context/CMakeFiles/boost_context.dir/src/asm/jump_x86_64_ms_pe_masm.asm.obj   
Microsoft (R) Macro Assembler (x64) Version 14.44.35215.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
MASM : fatal error A1013:invalid numerical command-line argument : /W  
```  
  
PR #294 claimed to fix this, but it didn't seem to do anything, neither did adding a `NOT MSVC` guard to that clang guard.  
  
For reference, the command being run (ignoring -I stuff):  
  
```  
C:\PROGRA~1\MICROS~1\2022\COMMUN~1\VC\Tools\MSVC\1444~1.352\bin\Hostx64\x64\ml64.exe  
	-DARCHITECTURE_x86_64=1  
	-DBOOST_CONTEXT_EXPORT=""  
	-DBOOST_CONTEXT_NO_LIB=""  
	-DBOOST_CONTEXT_SOURCE=""  
	-DBOOST_CONTEXT_STATIC_LINK=""  
	-DYUZU_ROOM  
	-Wno-unused-command-line-argument  
	-Wno-unsafe-buffer-usage  
	-Wno-unused-value  
	-Wno-extra-semi-stmt  
	-Wno-sign-conversion  
	-Wno-reserved-identifier  
	-Wno-deprecated-declarations  
	/nologo  
	-c  
	-Fo  
	_deps/boost-build/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_masm.asm.obj  
	C:/Users/Administrator/code/eden/.cache/cpm/boost/1.89.0/libs/context/src/asm/make_x86_64_ms_pe_masm.asm  
```

---

## Comment 1

> Username: crueter  
> Created at: 2025-09-08 01:44:19 UTC  
> Url: https://github.com/boostorg/context/issues/313#issuecomment-3264305361  

In our case, this was caused by some global options being passed to the assembler:  
```cmake  
    add_compile_options(  
        -Wno-unused-command-line-argument  
        -Wno-unsafe-buffer-usage  
        -Wno-unused-value  
        -Wno-extra-semi-stmt  
        -Wno-sign-conversion  
        -Wno-reserved-identifier  
        -Wno-deprecated-declarations  
    )  
```  
  
...which can be fixed by generator expressions:  
  
```cmake  
    add_compile_options(  
        $<$<COMPILE_LANGUAGE:C,CXX>:-Wno-unused-command-line-argument>  
        $<$<COMPILE_LANGUAGE:C,CXX>:-Wno-unsafe-buffer-usage>  
        $<$<COMPILE_LANGUAGE:C,CXX>:-Wno-unused-value>  
        $<$<COMPILE_LANGUAGE:C,CXX>:-Wno-extra-semi-stmt>  
        $<$<COMPILE_LANGUAGE:C,CXX>:-Wno-sign-conversion>  
        $<$<COMPILE_LANGUAGE:C,CXX>:-Wno-reserved-identifier>  
        $<$<COMPILE_LANGUAGE:C,CXX>:-Wno-deprecated-declarations>  
    )  
```
