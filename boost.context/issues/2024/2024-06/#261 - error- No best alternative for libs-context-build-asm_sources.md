# #261 - [VS17.10.1][amd64][x86] error: No best alternative for libs/context/build/asm_sources [Closed]

> Username: QuellaZhang  
> Created at: 2024-06-11 07:54:33 UTC  
> Updated at: 2024-06-14 08:08:41 UTC  
> Closed at: 2024-06-14 08:08:41 UTC  
> Url: https://github.com/boostorg/context/issues/261  

Hi All,  
  
When we build Boost source code on VS17.10.1, the following error occurs, but it used to pass on VS17.9.6. Can you take a look?  
  
**Build steps:**  
  
1. open x64 VS17.10.1 tools command  
2. git clone https://github.com/boostorg/boost.git C:\gitP\boostorg\boost  
3. cd /d C:\gitP\boostorg\boost  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64  
6. .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64  
  
**Error info:**  
```  
error: This module (predef/check/predef.jam) is OBSOLETE. Use predef/tools/check/predef.jam instead.  
Performing configuration checks  
  
    - default address-model    : none (cached) [1]  
    - default architecture     : none (cached) [1]  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
    - x86                      : no [2]  
    - arm                      : no [2]  
    - mips1                    : no [2]  
    - power                    : no [2]  
    - sparc                    : no [2]  
    - cxx11_static_assert      : no [2]  
    - x86                      : no [3]  
    - arm                      : no [3]  
    - mips1                    : no [3]  
    - power                    : no [3]  
    - sparc                    : no [3]  
    - cxx11_static_assert      : no [3]  
    - cxx11_variadic_templates : no [2]  
    - cxx11_variadic_templates : no [3]  
    - cxx11_hdr_ratio          : no [2]  
    - cxx11_hdr_ratio          : no [3]  
    - cxx20_hdr_concepts       : no [2]  
    - cxx20_hdr_concepts       : no [3]  
error: No best alternative for libs/context/build/asm_sources with <abi>ms <address-model>64 <asynch-exceptions>off   
<binary-format>pe <boost.cobalt.executor>any_io_executor <boost.cobalt.pmr>std <context-impl>fcontext <coverage>off   
<debug-store>object <debug-symbols>off <embed-manifest-via>linker <embed-manifest>on <exception-handling>on   
<extern-c-nothrow>off <inlining>full <link>static <midl-robust>yes <midl-stubless-proxy>yes <optimization>speed <os>  
NT <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <python>3.11 <relevant>abi <relevant>  
address-model <relevant>architecture <relevant>binary-format <relevant>toolset <rtti>on <runtime-debugging>off   
<runtime-link>shared <stdlib>native <strip>off <target-os>windows <testing.execute>on <threadapi>win32 <threading  
>multi <toolset-msvc:version>14.3 <toolset>msvc <variant>release <vectorize>off <visibility>hidden <warnings-as-errors>  
off <warnings>on <windows-api>desktop  
    no match: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
```

---

## Comment 1

> Username: QuellaZhang  
> Created at: 2024-06-14 08:08:41 UTC  
> Url: https://github.com/boostorg/context/issues/261#issuecomment-2167487197  

Adding architecture=x86 fixed this issue and closed the issue.
