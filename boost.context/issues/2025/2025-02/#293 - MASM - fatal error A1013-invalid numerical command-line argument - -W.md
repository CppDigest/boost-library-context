# #293 - MASM : fatal error A1013:invalid numerical command-line argument : /W [Closed]

> Username: marakew  
> Created at: 2025-02-21 19:55:41 UTC  
> Updated at: 2025-04-17 04:26:43 UTC  
> Closed at: 2025-04-17 04:26:43 UTC  
> Url: https://github.com/boostorg/context/issues/293  

https://github.com/boostorg/context/blob/afd2ce33722e223d75723f3b44dc9fa94302e764/CMakeLists.txt#L192-L193  
  
i use clan-cl as CMAKE_CXX_COMPILER_ID  
but masm is ml64.exe  
  
```  
[1/198] D:\PROGRA~1\MICROS~1\2019\ENTERP~1\VC\Tools\MSVC\1429~1.301\bin\Hostx64\x64\ml64.exe -DBOOST_CLANG=1 -DBOOST_CONTEXT_EXPORT="" -DBOOST_CONTEXT_NO_LIB="" -DBOOST_CONTEXT_SOURCE="" -DBOOST_CONTEXT_STATIC_LINK="" -D_CRT_SECURE_NO_WARNINGS -ID:\git\boost_\libs\context\include -ID:\git\boost_\libs\assert\include -ID:\git\boost_\libs\config\include -ID:\git\boost_\libs\core\include -ID:\git\boost_\libs\static_assert\include -ID:\git\boost_\libs\throw_exception\include -ID:\git\boost_\libs\mp11\include -ID:\git\boost_\libs\pool\include -ID:\git\boost_\libs\integer\include -ID:\git\boost_\libs\type_traits\include -ID:\git\boost_\libs\winapi\include -ID:\git\boost_\libs\predef\include -ID:\git\boost_\libs\smart_ptr\include -I"D:\Program Files (x86)\Microsoft Visual Studio\2019\EnterprisePreview\VC\Tools\MSVC\14.29.30133\include" -I"D:\Program Files (x86)\Microsoft Visual Studio\2019\EnterprisePreview\VC\Tools\MSVC\14.29.30133\atlmfc\include" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\shared" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\winrt" /nologo /quiet -Wno-unused-command-line-argument -c -Fo boost\libs\context\CMakeFiles\boost_context.dir\src\asm\jump_x86_64_ms_pe_masm.asm.obj D:\git\boost_\libs\context\src\asm\jump_x86_64_ms_pe_masm.asm  
FAILED: boost/libs/context/CMakeFiles/boost_context.dir/src/asm/jump_x86_64_ms_pe_masm.asm.obj   
D:\PROGRA~1\MICROS~1\2019\ENTERP~1\VC\Tools\MSVC\1429~1.301\bin\Hostx64\x64\ml64.exe -DBOOST_CLANG=1 -DBOOST_CONTEXT_EXPORT="" -DBOOST_CONTEXT_NO_LIB="" -DBOOST_CONTEXT_SOURCE="" -DBOOST_CONTEXT_STATIC_LINK="" -D_CRT_SECURE_NO_WARNINGS -ID:\git\boost_\libs\context\include -ID:\git\boost_\libs\assert\include -ID:\git\boost_\libs\config\include -ID:\git\boost_\libs\core\include -ID:\git\boost_\libs\static_assert\include -ID:\git\boost_\libs\throw_exception\include -ID:\git\boost_\libs\mp11\include -ID:\git\boost_\libs\pool\include -ID:\git\boost_\libs\integer\include -ID:\git\boost_\libs\type_traits\include -ID:\git\boost_\libs\winapi\include -ID:\git\boost_\libs\predef\include -ID:\git\boost_\libs\smart_ptr\include -I"D:\Program Files (x86)\Microsoft Visual Studio\2019\EnterprisePreview\VC\Tools\MSVC\14.29.30133\include" -I"D:\Program Files (x86)\Microsoft Visual Studio\2019\EnterprisePreview\VC\Tools\MSVC\14.29.30133\atlmfc\include" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\shared" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um" -I"D:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\winrt" /nologo /quiet -Wno-unused-command-line-argument -c -Fo boost\libs\context\CMakeFiles\boost_context.dir\src\asm\jump_x86_64_ms_pe_masm.asm.obj D:\git\boost_\libs\context\src\asm\jump_x86_64_ms_pe_masm.asm  
Microsoft (R) Macro Assembler (x64) Version 14.29.30154.0  
  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
  
  
MASM : warning A4018:invalid command-line option : /quiet  
  
MASM : fatal error A1013:invalid numerical command-line argument : /W  
  
```

---

## Comment 1

> Username: Challanger524  
> Created at: 2025-02-22 00:50:23 UTC  
> Updated at: 2025-02-22 01:07:52 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2675890286  

Looks like we need `CMAKE_<LANG>_SIMULATE_ID` and/or `CMAKE_<LANG>_COMPILER_FRONTEND_VARIANT` to detect `clang-cl` (`MSVC`) and not to pass `-Wno-unused-command-line-argument`  
https://discourse.cmake.org/t/how-to-determine-if-clang-or-clang-cl-is-used-on-windows/4129  
```cmake  
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang" AND (NOT (CMAKE_CXX_SIMULATE_ID STREQUAL "MSVC")))  
```  
  
But why `/quiet` is not working!?  
```cmake  
if(MSVC AND NOT(MSVC_VERSION LESS 1936)) # Visual Studio 2022 version 17.6  
      set_property(SOURCE ${ASM_SOURCES} APPEND PROPERTY COMPILE_OPTIONS "/quiet")  
```  
  
can you change `1936` -> `1937` and check it?

---

## Comment 2

> Username: marakew  
> Created at: 2025-02-22 08:42:55 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676097702  

1937 works  
and check simulate_id works  
  
but i dont like your logic  
your check cxx compiler  
but set options for asm files

---

## Comment 3

> Username: Challanger524  
> Created at: 2025-02-22 09:29:32 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676118895  

maybe `set(BOOST_CONTEXT_ASSEMBLER "${_default_asm}"..` should be used then!?

---

## Comment 4

> Username: Challanger524  
> Created at: 2025-02-22 10:02:19 UTC  
> Updated at: 2025-02-22 14:00:23 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676129189  

> but i dont like your logic, your check cxx compiler, but set options for asm files  
  
```cmake  
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")  
  set_property(SOURCE ${ASM_SOURCES} APPEND PROPERTY COMPILE_OPTIONS "-Wno-unused-command-line-argument")  
```  
was added (by me) in the same manner as  
```cmake  
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")  
  set_property(SOURCE ${ASM_SOURCES} APPEND PROPERTY COMPILE_OPTIONS "-x" "assembler-with-cpp")  
```  
that was existed before me.  
  
`elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")`  
should then be (I suppose)  
`elseif(BOOST_CONTEXT_ARCHITECTURE STREQUAL "armclang")`

---

## Comment 5

> Username: Challanger524  
> Created at: 2025-02-22 13:59:11 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676225159  

Managed to setup `Clang-CL` in VS Code and reproduce the issue, will fix soonely.  
  
@marakew how did you setup your clang-cl? `vcvarsall.bat` does not setup `mt.exe` path so it fails with `MT: command "CMAKE_MT-NOTFOUND ...` and vscode cmake-tools failed with `lld-link: error: <root>: undefined symbol: mainCRTStartup` without `"cmake.useVsDeveloperEnvironment": "always"`

---

## Comment 6

> Username: Challanger524  
> Created at: 2025-02-22 15:21:18 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676259376  

> 1937 works  
  
A bit unclear what you mean by "works". You want to say that your `message(STATUS "MSVC_VERSION: ${MSVC_VERSION}")` is exactly `1936` but MASM fails to understand `/quiet`? I had concern coz author of this option stated 17.7 [here](https://gitlab.kitware.com/cmake/cmake/-/issues/23537#note_1316785), but [doc](https://learn.microsoft.com/en-us/cpp/assembler/masm/ml-and-ml64-command-line-reference?view=msvc-170#:~:text=Available%20in%20Visual%20Studio%2017.6%20and%20later) says 17.6:  
  
>Concern  
>It is better to check if we can pass /quiet on any 17.6 patch version, or better to set 1937 to be 100% sure that it works.

---

## Comment 7

> Username: Challanger524  
> Created at: 2025-02-22 15:29:46 UTC  
> Updated at: 2025-02-22 15:51:24 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676262671  

>i have own toolchain and build cmd  
  
I am doing it with as least amount of custom setups as possible, like:  
`CMakePresets.json`:  
```json  
    {  
      "name": "base",  
      "hidden": true,  
      "generator": "Ninja",  
      "binaryDir":  "${sourceDir}/out/build/${presetName}",  
      "installDir": "${sourceDir}/out/install/${presetName}",  
      "cacheVariables": {  
        "CMAKE_BUILD_TYPE": "MinSizeRel",  
      }  
    },  
    {  
      "name": "clang-cl-dbg",  
      "displayName": "Clang-CL - debug",  
      "inherits": "base",  
      "architecture": { "strategy": "external", "value":      "x64" },  
      "toolset":      { "strategy": "external", "value": "host=x64" },  
      "cacheVariables": {  
        "CMAKE_C_COMPILER"  : "clang-cl",  
        "CMAKE_CXX_COMPILER": "clang-cl",  
        "CMAKE_BUILD_TYPE": "Debug"  
      }  
    },  
```  
```cmake  
set(BOOST_SOURCE_DIR_ "${CMAKE_SOURCE_DIR}/../boost")  
set(BOOST_INCLUDE_LIBRARIES beast)  
add_subdirectory("${BOOST_SOURCE_DIR_}" "${CMAKE_CURRENT_BINARY_DIR}/dep/boost" EXCLUDE_FROM_ALL)  
target_link_libraries(${PROJECT_NAME} PRIVATE Boost::beast)  
if (MINGW)  
  target_link_libraries(boost_asio INTERFACE ws2_32)  
endif()  
```

---

## Comment 8

> Username: marakew  
> Created at: 2025-02-22 15:52:41 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676274529  

-- MSVC_VERSION: 1936  
when i set  1937 is works without /quiet  
  
this is VS 2019  
  
```  
D:\…sePreview\VC\Tools\MSVC\14.29.30133\bin\Hostx64\x64>ml64.exe /?  
Microsoft (R) Macro Assembler (x64) Version 14.29.30154.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
        ML64 [ /options ] filelist [ /link linkoptions ]  
  
/Bl<linker> Use alternate linker          /Sf Generate first pass listing  
/c Assemble without linking               /Sl<width> Set line width  
/Cp Preserve case of user identifiers     /Sn Suppress symbol-table listing  
/Cx Preserve case in publics, externs     /Sp<length> Set page length  
/D<name>[=text] Define text macro         /Ss<string> Set subtitle  
/EP Output preprocessed listing to stdout /St<string> Set title  
/F <hex> Set stack size (bytes)           /Sx List false conditionals  
/Fe<file> Name executable                 /Ta<file> Assemble non-.ASM file  
/Fl[file] Generate listing                /w Same as /W0 /WX  
/Fm[file] Generate map                    /WX Treat warnings as errors  
/Fo<file> Name object file                /W<number> Set warning level  
/Fr[file] Generate limited browser info   /X Ignore INCLUDE environment path  
/FR[file] Generate full browser info      /Zd Add line number debug info  
/I<name> Add include path                 /Zf Make all symbols public  
/link <linker options and libraries>      /Zi Add symbolic debug info  
/nologo Suppress copyright message        /Zp[n] Set structure alignment  
/Sa Maximize source listing               /Zs Perform syntax check only  
/ZH:SHA_256 Use SHA256 for checksum  
    in debug info (experimental)  
/Gy[-] separate functions for linker  
/errorReport:<option> Deprecated. Report internal assembler errors to Microsoft  
    none - do not send report  
    prompt - prompt to immediately send report  
    queue - at next admin logon, prompt to send report  
    send - send report automatically  
```  
VS about says this is  
Microsoft Visual Studio Enterprise 2019  
Version 16.11.35

---

## Comment 9

> Username: Challanger524  
> Created at: 2025-02-22 16:18:32 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676287759  

It is super strange since `NOT(MSVC_VERSION LESS 1936)` should apply `/quiet` only for `MSVC_VERSION >= 1936`, but your Visual Studio is way older than 17.6 - "Microsoft Visual Studio Enterprise 2019 Version 16.11.35"  
Mine is: `[cmake] -- MSVC_VERSION: 1939`  
  
```  
PS ...\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.39.33519\bin\Hostx64\x64> .\ml64.exe /?  
Microsoft (R) Macro Assembler (x64) Version 14.39.33523.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
        ML64 [ /options ] filelist [ /link linkoptions ]  
  
...  
/ZH:SHA_256 Use SHA256 for checksum in debug info (default)  
/Gy[-] separate functions for linker      /quiet Suppress 'Assembling' message <<<---------  
...  
```  
Not sure what to do with `1936`, probably will change it to `1937` and ask that guy from CMake forum about Visual Studio vs ML versioning vs Doc validity

---

## Comment 10

> Username: marakew  
> Created at: 2025-02-22 16:59:35 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676307231  

VS 2022 LTS 17.6.14  
  
```  
D:\…sePreview\VC\Tools\MSVC\14.36.32532\bin\Hostx64\x64>ml64.exe /?  
Microsoft (R) Macro Assembler (x64) Version 14.36.32545.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
        ML64 [ /options ] filelist [ /link linkoptions ]  
  
/Bl<linker> Use alternate linker          /Sf Generate first pass listing  
/c Assemble without linking               /Sl<width> Set line width  
/Cp Preserve case of user identifiers     /Sn Suppress symbol-table listing  
/Cx Preserve case in publics, externs     /Sp<length> Set page length  
/D<name>[=text] Define text macro         /Ss<string> Set subtitle  
/EP Output preprocessed listing to stdout /St<string> Set title  
/F <hex> Set stack size (bytes)           /Sx List false conditionals  
/Fe<file> Name executable                 /Ta<file> Assemble non-.ASM file  
/Fl[file] Generate listing                /w Same as /W0 /WX  
/Fm[file] Generate map                    /WX Treat warnings as errors  
/Fo<file> Name object file                /W<number> Set warning level  
/Fr[file] Generate limited browser info   /X Ignore INCLUDE environment path  
/FR[file] Generate full browser info      /Zd Add line number debug info  
/I<name> Add include path                 /Zf Make all symbols public  
/link <linker options and libraries>      /Zi Add symbolic debug info  
/nologo Suppress copyright message        /Zp[n] Set structure alignment  
/Sa Maximize source listing               /Zs Perform syntax check only  
/ZH:MD5 Use MD5 for checksum in debug info  
/ZH:SHA_256 Use SHA256 for checksum in debug info (default)  
/Gy[-] separate functions for linker      /quiet Suppress 'Assembling' message  
/errorReport:<option> Deprecated. Report internal assembler errors to Microsoft  
    none - do not send report  
    prompt - prompt to immediately send report  
    queue - at next admin logon, prompt to send report  
    send - send report automatically  
```

---

## Comment 11

> Username: marakew  
> Created at: 2025-02-22 17:05:44 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676309678  

> Not sure what to do with 1936, probably will change it to 1937 and ask that guy from CMake forum about Visual Studio vs ML versioning vs Doc validity  
  
its depend how cmake detect version  
may be it read form windows reg  
installed VS != use VS

---

## Comment 12

> Username: Challanger524  
> Created at: 2025-02-22 17:17:59 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676314216  

Are you using VS Code? https://github.com/microsoft/vscode-cmake-tools/issues/1053  
  
>When I add 'VCToolsInstallDir' to MSVC_ENVIRONMENT_VARIABLES array in \.vscode\extensions\ms-vscode.cmake-tools-1.3.0\dist\main.js(or src/kit.ts) of extension.  
and reconfigure my project. It works as expected. The cmake variable ${MSVC_VERSION} is correct.

---

## Comment 13

> Username: marakew  
> Created at: 2025-02-22 17:22:39 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676316015  

No I don't use it

---

## Comment 14

> Username: Challanger524  
> Created at: 2025-02-22 17:27:57 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676317921  

At least `/quiet` flag gives warning and not an error, so in case of further incorrect CMake `MSVC_VERSION` detecting - it is not critical but probably should be discussed. @olk what you think?

---

## Comment 15

> Username: marakew  
> Created at: 2025-02-22 17:35:44 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676320713  

D:\git\A_BUILD\cmake\Modules\Platform\Windows-Clang.cmake  
  
```  
if("${CMAKE_${lang}_SIMULATE_VERSION}" MATCHES "^([0-9]+)\\.([0-9]+)")  
  math(EXPR MSVC_VERSION "${CMAKE_MATCH_1}*100 + ${CMAKE_MATCH_2}")  
endif()  
```  
  
in general we can't rely on MSVC_VERSION when clang-cl is used

---

## Comment 16

> Username: Challanger524  
> Created at: 2025-02-22 17:55:59 UTC  
> Updated at: 2025-02-22 17:56:39 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676328131  

Probably `/quiet` support for `clang-cl` should be dropped since we can't be sure about real `MSVC_VERSION`:  
```cmake  
if(MSVC AND NOT(MSVC_VERSION LESS 1937) AND NOT(CMAKE_C_SIMULATE_VERSION) AND NOT(CMAKE_CXX_SIMULATE_VERSION))  
  set_property(SOURCE ${ASM_SOURCES} APPEND PROPERTY COMPILE_OPTIONS "/quiet")  
```

---

## Comment 17

> Username: marakew  
> Created at: 2025-02-22 18:03:01 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676330741  

you didn't get me  
you can't rely on the MSVC_VERSION variable in setting the option for masm ml  
because I can, for example, change ml64 to a newer version  
so what? all your checks will go to waste

---

## Comment 18

> Username: marakew  
> Created at: 2025-02-22 18:07:25 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676332500  

in a special case  
I can not install VS at all  
and just pull a separate masm package from VS  
and use ml64 from there  
your checks will go to waste again

---

## Comment 19

> Username: Challanger524  
> Created at: 2025-02-22 18:14:30 UTC  
> Updated at: 2025-02-22 18:21:11 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676335074  

```  
[cmake] -- CMAKE_CXX_SIMULATE_VERSION: 19.39  
[cmake] -- MSVC_VERSION: 1939  
```  
That's exactly why I want to drop it, in order to know for sure - assembler version is needed, but there no easy way to get it, only by executing `<assembler> --version` and parsing the output, which is unlikely will be accepted in PR.

---

## Comment 20

> Username: Challanger524  
> Created at: 2025-02-22 18:55:02 UTC  
> Updated at: 2025-02-23 08:21:15 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676349664  

@marakew can you tell (somewhere) your `noreply` GitHub email so I can add you as commit co-author? (not the private email)  
https://docs.github.com/en/pull-requests/committing-changes-to-your-project/creating-and-editing-commits/creating-a-commit-with-multiple-authors#required-co-author-information

---

## Comment 21

> Username: Challanger524  
> Created at: 2025-02-23 13:16:09 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676855182  

Response (Y/n)? Keeping PR draft for a day more

---

## Comment 22

> Username: marakew  
> Created at: 2025-02-23 13:19:10 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2676856528  

no thanks

---

## Comment 23

> Username: Challanger524  
> Created at: 2025-02-24 13:27:40 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2678417823  

@marakew found a way to load Visual Studio environment in CMake: https://github.com/Challanger524/test-boost-beast/blob/main/msvcToolchain.cmake

---

## Comment 24

> Username: marakew  
> Created at: 2025-02-24 13:38:29 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2678444587  

I don't look for ways to integrate VS  
I don't need it  
You probably forget that there are many scenarios when it is not necessary  
When using wine on Linux or any other OS  
Even a separate utility, be it cl or ml or rc, can be taken separately without using the entire toolchain or the entire VS  
  
One way or another, I customize the entire scenario  
Since mine is very diverse

---

## Comment 25

> Username: olk  
> Created at: 2025-04-17 04:24:35 UTC  
> Url: https://github.com/boostorg/context/issues/293#issuecomment-2811702085  

PR #294 merged
