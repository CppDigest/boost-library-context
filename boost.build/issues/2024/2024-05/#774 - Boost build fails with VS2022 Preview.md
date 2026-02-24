# #774 - Boost build fails with VS2022 Preview [Closed]

> Username: pps83  
> Created at: 2024-05-15 13:15:49 UTC  
> Updated at: 2024-05-15 13:16:02 UTC  
> Closed at: 2024-05-15 13:16:01 UTC  
> Url: https://github.com/boostorg/build/issues/774  

Here's sample output:  
  
```  
D:\work-pps\boost_1_85_0>.\b2 address-model=32,64 variant=debug,release link=static threading=multi runtime-link=static stage  
Performing configuration checks  
  
    - default address-model    : none [1]  
    - default architecture     : none [1]  
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
error: No best alternative for libs/context/build/asm_sources with <abi>ms <address-model>32 <asynch-exceptions>off <binary-format>pe <boost.cobalt.executor>any_io_executor <boost.cobalt.pmr>std <context-impl>fcontext <coverage>off <debug-store>object <debug-symbols>on <embed-manifest-via>linker <embed-manifest>on <exception-handling>on <extern-c-nothrow>off <inlining>off <link>static <midl-robust>yes <midl-stubless-proxy>yes <optimization>off <os>NT <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <relevant>abi <relevant>address-model <relevant>architecture <relevant>binary-format <relevant>toolset <rtti>on <runtime-debugging>on <runtime-link>static <stdlib>native <strip>off <target-os>windows <testing.execute>on <threadapi>win32 <threading>multi <toolset-msvc:version>14.3 <toolset>msvc <variant>debug <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on <windows-api>desktop  
...  
```

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2024-05-15 13:16:00 UTC  
> Url: https://github.com/boostorg/build/issues/774#issuecomment-2112491308  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
