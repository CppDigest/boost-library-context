# #295 - Error for multiple `-arch` flags [Open]

> Username: danieljames  
> Created at: 2018-04-04 09:42:31 UTC  
> Updated at: 2021-06-26 03:10:16 UTC  
> Url: https://github.com/boostorg/build/issues/295  

From [a user list mail](https://lists.boost.org/boost-users/2018/04/88493.php):  
  
> I got this error:   
>  
>     ./b2 toolset=clang 'cxxflags=-arch i386 -arch x86_64'   
>  
>     /Users/gjasny/Git/ExternalLibs/boost/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/configure.jam:288: in try-find-build   
>     *** argument error   
>     * rule log-check-result ( result )   
>     * called with: ( )   
>     * missing argument result   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/configure.jam:86:see definition of rule 'log-check-result' being called   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/configure.jam:391: in find-builds-raw   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/configure.jam:450: in configure.find-builds   
>     /Users/gjasny/Git/ExternalLibs/boost/boostcpp.jam:690: in boostcpp.deduce-address-model   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/kernel/modules.jam:107: in modules.call-in   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/util/indirect.jam:105: in indirect.call   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/property.jam:144: in property.evaluate-conditionals-in-context   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/targets.jam:1087: in evaluate-requirements   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/targets.jam:1121: in common-properties2   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/targets.jam:1017: in targets.common-properties   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/targets.jam:1313: in alias-target-class.generate   
>     /Users/gjasny/Git/ExternalLibs/boost/boostcpp.jam:500: in build-multiple   
>     /Users/gjasny/Git/ExternalLibs/boost/boostcpp.jam:490: in class_at_top-level-target.generate   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/targets.jam:812: in generate-really   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/targets.jam:784: in class_at_main-target.generate   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build/targets.jam:273: in class_at_project-target.generate   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/build-system.jam:797: in load   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/kernel/modules.jam:295: in import   
>     /Users/gjasny/Git/ExternalLibs/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build   
>     /Users/gjasny/Git/ExternalLibs/boost/boost-build.jam:17: in module scope   
>  
> I guess b2 when trying to deduce compiler settings stumbles over  
> 'cxxflags=-arch i386 -arch x86_64'. In case that's accepted behavior,  
> how could I specify architectures for a fat library?

---

## Comment 1

> Username: danieljames  
> Created at: 2018-04-04 12:09:36 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-378577771  

Sorry, I forgot to mention that this is in the 1.67.0 beta. It'd be helpful if someone could look at this soonish, so we can tell if it needs to be fixed in the release.

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-04-04 15:05:17 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-378633596  

AMDG  
  
On 04/04/2018 06:09 AM, Daniel James wrote:  
> Sorry, I forgot to mention that this is in the 1.67.0 beta. It'd be helpful if someone could look at this soonish, so we can tell if it needs to be fixed in the release.  
>   
  
I think address-model=32_64 is supposed to  
achieve this.  I'll try to fix this some time  
today.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: danieljames  
> Created at: 2018-04-07 11:38:56 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-379463254  

Will this be ready soon? I can delay the release a little if needed.

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-04-07 14:15:24 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-379472505  

AMDG  
  
On 04/07/2018 05:38 AM, Daniel James wrote:  
> Will this be ready soon? I can delay the release a little if needed.  
>   
  
  It looks like it's good in develop, though I'd appreciate  
it if someone with a mac can check that it actually fixes  
the original problem.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: Belcourt  
> Created at: 2018-04-08 16:28:06 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-379562968  

My Mac does this:  
  
kbelco$ ./b2 toolset=clang 'cxxflags=-arch i386 -arch x86_64'   
/Users/kbelco/Projects/boost/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : none  
    - default architecture     : x86  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
error: No best alternative for libs/context/build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>power <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>x86 <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <architecture>combined <binary-format>mach-o <threading>multi  
        not matched  
    - C++11 mutex              : yes  
    - lockfree boost::atomic_flag : yes  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_final : yes  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes  
    - Boost.Config Feature Check: cxx11_hdr_regex : yes  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
    - Boost.Config Feature Check: cxx11_noexcept : yes  
    - Boost.Config Feature Check: cxx11_nullptr : yes  
    - Boost.Config Feature Check: cxx11_rvalue_references : yes  
    - Boost.Config Feature Check: cxx11_template_aliases : yes  
    - Boost.Config Feature Check: cxx11_thread_local : yes  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes  
    - has_icu builds           : no  
    - zlib                     : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zstd                     : no  
    - iconv (libc)             : no  
    - iconv (separate)         : yes  
    - icu                      : no  
    - icu (lib64)              : no  
    - native-atomic-int32-supported : yes  
    - native-syslog-supported  : yes  
    - pthread-supports-robust-mutexes : no  
    - compiler-supports-visibility : yes  
    - compiler-supports-ssse3  : yes  
    - compiler-supports-avx2   : yes  
    - gcc visibility           : yes  
    - long double support      : yes  
    - libbacktrace builds      : no  
    - addr2line builds         : yes  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
error: No best alternative for libs/context/build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>power <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>x86 <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <architecture>combined <binary-format>mach-o <threading>multi  
        not matched  
    - zlib                     : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zstd                     : no  
  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : building  
    - contract                 : building  
    - coroutine                : building  
    - date_time                : building  
    - exception                : building  
    - fiber                    : building  
    - filesystem               : building  
    - graph                    : building  
    - graph_parallel           : building  
    - iostreams                : building  
    - locale                   : building  
    - log                      : building  
    - math                     : building  
    - mpi                      : building  
    - program_options          : building  
    - python                   : building  
    - random                   : building  
    - regex                    : building  
    - serialization            : building  
    - signals                  : building  
    - stacktrace               : building  
    - sync                     : building  
    - system                   : building  
    - test                     : building  
    - thread                   : building  
    - timer                    : building  
    - type_erasure             : building  
    - wave                     : building  
  
...patience...  
...patience...  
...patience...  
...patience...  
...patience...  
...found 25741 targets...  
...updating 1292 targets...

---

## Comment 6

> Username: danieljames  
> Created at: 2018-04-09 12:06:20 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-379728982  

Is this ready for 1.67.0?

---

## Comment 7

> Username: swatanabe  
> Created at: 2018-04-09 14:56:14 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-379781373  

AMDG  
  
On 04/09/2018 06:06 AM, Daniel James wrote:  
> Is this ready for 1.67.0?  
>   
  
Yes.  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: danieljames  
> Created at: 2018-04-09 15:12:48 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-379787222  

OK thanks, I created a branch from master for your recent commits and will use that in the release:  
  
https://github.com/boostorg/build/commits/fixes/1.67

---

## Comment 9

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:34 UTC  
> Url: https://github.com/boostorg/build/issues/295#issuecomment-868936492  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
