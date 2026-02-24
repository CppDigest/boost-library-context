# #289 - Boost 1.87 undefined jump_fcontext on loongarch platform [Closed]

> Username: setarcos  
> Created at: 2025-02-09 09:52:49 UTC  
> Updated at: 2025-05-05 00:40:55 UTC  
> Closed at: 2025-05-05 00:40:54 UTC  
> Url: https://github.com/boostorg/context/issues/289  

The resulting libboost_context.so has undefined symble for jump_fcontext, make_fcontext and ontop_fcontext.  
  
```  
nm -D usr/lib/libboost_context.so | grep jump_fcontext  
                 U jump_fcontext  
0000000000000ba0 T _ZN5boost7context6detail13jump_fcontextEPvS2_  
```  
and the building system complains about  
```  
error: No best alternative for /build/boost/src/boost_1_87_0/libs/context/build/asm_sources with <abi>aapcs <address-model>64 <architecture>loongarch <asynch-exceptions>off <binary-format>elf <boost.beast.allow-deprecated>on <boost.beast.separate-compilation>on <boost.cobalt.executor>any_io_executor <boost.cobalt.pmr>std <context-impl>fcontext <coverage>off <cxxstd-dialect>iso <cxxstd>23 <debug-symbols>off <exception-handling>on <extern-c-nothrow>off <inlining>full <known-warnings>hide <link>static <optimization>speed <os>LINUX <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <python>3.13 <relevant>abi <relevant>address-model <relevant>architecture <relevant>binary-format <relevant>toolset <rtti>on <runtime-debugging>off <runtime-link>shared <stdlib>native <strip>off <target-os>linux <testing.execute>on <threadapi>pthread <threading>multi <toolset-gcc:version>14 <toolset>gcc <variant>release <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on <x-deduced-platform>loongarch_64  
```  
The abi for loongarch is clearly wrong. following code in libs/context/boost-context-features.jam may be related.  
  
```  
local rule default_abi ( )  
{  
    local tmp = sysv ;  
    if [ os.name ] = "NT" { tmp = ms ; }  
    else if [ os.name ] = "CYGWIN" { tmp = ms ; }  
    else if [ os.platform ] in "ARM" "ARM64" { tmp = aapcs ; }  
    else if [ os.platform ] = "MIPS32" { tmp = o32 ; }  
    else if [ os.platform ] = "MIPS64" { tmp = n64 ; }  
    return $(tmp) ;  
}  
```  
On my loongarch platform, the os.platform variable is empty,  and above function return `aapcs`.  Force the function return sysv gives correct so file.

---

## Comment 1

> Username: setarcos  
> Created at: 2025-05-05 00:40:54 UTC  
> Url: https://github.com/boostorg/context/issues/289#issuecomment-2849628769  

Commit 63996e4 fixed this before my issue.
