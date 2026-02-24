# #254 - Failure to assemble ARM64 code with MSVC's ARMASM64 [Open]

> Username: hmartinez82  
> Created at: 2024-03-05 12:52:23 UTC  
> Updated at: 2025-06-28 18:13:43 UTC  
> Url: https://github.com/boostorg/context/issues/254  

Boost 1.84  
CMake 3.28.3  
Visual Studio 2022 17.9.2  
Ninja 1.11.1  
  
This is building Boost Context on a ARM64 machine. Not x-compiling.  
  
### Description  
  
Failure to assemble ARM64 code:  
```  
[0/2] Re-checking globbed directories...  
[1/2] Building ASM_ARMASM object libs\context\CMakeFiles\boost_context.dir\src\asm\ontop_arm64_aapcs_pe_armasm.asm.obj  
FAILED: libs/context/CMakeFiles/boost_context.dir/src/asm/ontop_arm64_aapcs_pe_armasm.asm.obj  
C:\PROGRA~1\MICROS~1\2022\PROFES~1\VC\Tools\MSVC\1439~1.335\bin\HOSTAR~1\arm64\armasm64.exe -DBOOST_CONTEXT_EXPORT="" -DBOOST_CONTEXT_NO_LIB="" -DBOOST_CONTEXT_SOURCE="" -DBOOST_CONTEXT_STATIC_LINK="" -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\context\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\assert\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\config\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\core\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\static_assert\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\throw_exception\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\mp11\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\pool\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\integer\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\type_traits\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\winapi\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\predef\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\smart_ptr\include -IC:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\move\include  -o libs\context\CMakeFiles\boost_context.dir\src\asm\ontop_arm64_aapcs_pe_armasm.asm.obj C:\Dev\Github\PrusaSlicer\deps\build\dep_Boost-prefix\src\dep_Boost\libs\context\src\asm\ontop_arm64_aapcs_pe_armasm.asm  
Microsoft (R) ARM Macro Assembler Version 14.39.33521.0 for 64 bits  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
error A2029: unknown command-line argument or argument value -DBOOST_CONTEXT_EXPORT=  
  
 Usage:      armasm [<options>] sourcefile objectfile  
             armasm [<options>] -o objectfile sourcefile  
             armasm -h              for help  
  
ninja: build stopped: subcommand failed.  
```  
### Possible explanation  
  
It seems that the Microsoft ARM Assembler is very poor of command line options:  
```  
Microsoft (R) ARM Macro Assembler Version 14.39.33521.0 for 64 bits  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
 Usage:      armasm [<options>] sourcefile objectfile  
             armasm [<options>] -o objectfile sourcefile  
             armasm -h              for help  
  
<options>:            (Upper case shows allowable abbreviation)  
  -Errors     errorsfile       redirect stderr diagnostics to errorsfile  
  -I          dir[;dir]        add dirs to include search path  
  -PreDefine  directive        pre-execute a SET{L,A,S} directive  
  -NOWarn                      turn off warning messages  
  -ignore <warning-num>        don't report warning-num  
  -help                        help (this information)  
  -via <file>                  read further arguments from <file>  
  -machine <machine>           set the PE machine type field  
  -g                           generate debugging info  
  -gh:SHA1                     use SHA1 for file checksum in debug info  
  -gh:SHA_256                  use SHA256 for file checksum in debug info (default)  
  -funcOverride:<func>         emit function overriding support for <func>  
  -errorReport:<option>        deprecated. Report internal assembler errors to Microsoft  
      none - do not send report  
      prompt - prompt to immediately send report  
      queue - at next admin logon, prompt to send report (default)  
      send - send report automatically  
  
<machine>:  ARM64 | ARM64EC  
```  
  
I manually edited the `build.ninja` file to remove all the DEFINES section for the three targets that run ARMASM64 and that got me to the finish line.  
  
I unfortunately don't know CMake enough to be able to tell the build to not emit *any* DEFINE for the assembler calls :(

---

## Comment 1

> Username: olk  
> Created at: 2024-03-10 19:33:02 UTC  
> Url: https://github.com/boostorg/context/issues/254#issuecomment-1987339800  

maybe this is an issue for boost.build because boost.context does not handle assembler arguments directly (abstracted away by boost.build)

---

## Comment 2

> Username: dixyes  
> Created at: 2024-03-11 12:26:55 UTC  
> Url: https://github.com/boostorg/context/issues/254#issuecomment-1988325919  

cmake (itself and boost's cmakelists) needs some work to take use of armasm, I've only tried to build context with b2

---

## Comment 3

> Username: magnushakansson  
> Created at: 2024-12-18 20:24:00 UTC  
> Url: https://github.com/boostorg/context/issues/254#issuecomment-2552200921  

Is it the same issue as this https://gitlab.kitware.com/cmake/cmake/-/issues/24639?

---

## Comment 4

> Username: BenMoran98  
> Created at: 2025-06-28 05:19:51 UTC  
> Url: https://github.com/boostorg/context/issues/254#issuecomment-3014979335  

Any tricks to compile boost context for Windows ARM64?

---

## Comment 5

> Username: hmartinez82  
> Created at: 2025-06-28 05:27:11 UTC  
> Url: https://github.com/boostorg/context/issues/254#issuecomment-3014983508  

@BenMoran98 You'll have to pass a parameter in CMake (I don't recall how, it's been years) to tell if to use the `winfib` (Windows Fibers` based implementation of Boost.Context instead of the standard `fcontext` when compiling for ARM64 on Windows.

---

## Comment 6

> Username: BenMoran98  
> Created at: 2025-06-28 10:34:14 UTC  
> Url: https://github.com/boostorg/context/issues/254#issuecomment-3015170739  

> [@BenMoran98](https://github.com/BenMoran98) You'll have to pass a parameter in CMake (I don't recall how, it's been years) to tell if to use the `winfib` (Windows Fibers`based implementation of Boost.Context instead of the standard`fcontext` when compiling for ARM64 on Windows.  
  
Thanks for the quick tip! will try it out from my side and get back

---

## Comment 7

> Username: BenMoran98  
> Created at: 2025-06-28 18:13:43 UTC  
> Url: https://github.com/boostorg/context/issues/254#issuecomment-3015966998  

@hmartinez82   
I tried building it by using the following commands by ended up in undefined symbol errors:  
ARM, Boost is being configured to use Windows Fibers in boost::context)  
  
Final CMake arguments: ['-D BOOST_INSTALL_LAYOUT=versioned', '-D BOOST_ENABLE_CMAKE=ON', "-D BOOST_EXCLUDE_LIBRARIES='mpi;graph_parallel'", '-D BOOST_CONTEXT_IMPLEMENTATION=winfib', '-D BOOST_CONTEXT_ARCHITECTURE=arm64', '-D BOOST_CONTEXT_ABI=aapcs', '-D BOOST_CONTEXT_BINARY_FORMAT=pe', '-D BOOST_CONTEXT_ASSEMBLER=armasm']  
  
Errors:  
coroutine_context.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) struct boost::context::detail::transfer_t __cdecl boost::context::detail::jump_fcontext(void * const,void *)" (__imp_?jump_fcontext@detail@context@boost@@YA?AUtransfer_t@123@QEAXPEAX@Z) referenced in function "public: void * __cdecl boost::coroutines::detail::coroutine_context::jump(class boost::coroutines::detail::coroutine_context &,void *)" (?jump@coroutine_context@detail@coroutines@boost@@QEAAPEAXAEAV1234@PEAX@Z) [C:\Users\mcw\Desktop\Mugundan\FreeCAD-LibPack\working\boost\build-release\libs\coroutine\boost_coroutine.vcxproj]  
         coroutine_context.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) void * __cdecl boost::context::detail::make_fcontext(void *,unsigned __int64,void (__cdecl*)(struct boost::context::detail::transfer_t))" (__imp_?make_fcontext@detail@context@boost@@YAPEAXPEAX_KP6AXUtransfer_t@123@@Z@Z) referenced in function "public: __cdecl boost::coroutines::detail::coroutine_context::coroutine_context(void (__cdecl*)(struct boost::context::detail::transfer_t),struct boost::coroutines::detail::preallocated const &)" (??0coroutine_context@detail@coroutines@boost@@QEAA@P6AXUtransfer_t@1context@3@@ZAEBUpreallocated@123@@Z) [C:\Users\mcw\Desktop\Mugundan\FreeCAD-LibPack\working\boost\build-release\libs\coroutine\boost_coroutine.vcxproj]  
         C:\Users\mcw\Desktop\Mugundan\FreeCAD-LibPack\working\boost\build-release\stage\bin\Release\boost_coroutine-vc143-mt-a64-1_88.dll : fatal error LNK1120: 2 unresolved externals [C:\Users\mcw\Desktop\Mugundan\FreeCAD-LibPack\working\boost\build-release\libs\coroutine\boost_coroutine.vcxproj]  
  
`
