# #241 - Not able to build for windows ARM64 with msvc toolset [Closed]

> Username: bansalaj  
> Created at: 2023-09-06 12:57:27 UTC  
> Updated at: 2024-03-10 19:29:03 UTC  
> Closed at: 2024-03-10 19:29:03 UTC  
> Url: https://github.com/boostorg/context/issues/241  

I'm trying to build boost libraries (1.82) for windows arm64 with msvc toolset but getting following error using b2 with cross compilation  
  
Build is happening on win64 machine.  
>coroutine_context.obj : error LNK2019: unresolved external symbol __imp_jump_fcontext referenced in function "public: void * __cdecl boost::coroutines::detail::coroutine_context::jump(class boost::coroutines::detail::coroutine_context &,void *)" (?jump@coroutine_context@detail@coroutines@boost@@QEAAPEAXAEAV1234@PEAX@Z)  
>coroutine_context.obj : error LNK2019: unresolved external symbol __imp_make_fcontext referenced in function "public: __cdecl boost::coroutines::detail::coroutine_context::coroutine_context(void (__cdecl*)(struct boost::context::detail::transfer_t),struct boost::coroutines::detail::preallocated const &)" (??0coroutine_context@detail@coroutines@boost@@QEAA@P6AXUtransfer_t@1context@3@@ZAEBUpreallocated@123@@Z)  
  
 ```  
MSVC_PLATFORM := ARM64  
MSVC_PLATFORM_TOOLSET := v143  
MSVC_SUFFIX := vc140  
MSVC_TOOL_ARCH := x64  
MSVC_VERSION := msvc-14.3  
MSVC_VERSION_ID := vc143  
MSVC_VERSION_NUM := 17.0  
```  
  
```  
b2         INFO         [1] msvc-17.0  
  
b2         INFO         [2] msvc-17.0/release/abi-aapcs/architecture-arm/asynch-exceptions-on/threadapi-win32/threading-multi/visibility-hidden  
  
b2         INFO         [3] msvc-17.0/release/abi-aapcs/architecture-arm/asynch-exceptions-on/link-static/threadapi-win32/threading-multi/visibility-hidden  
b2         INFO           
b2         INFO         Component configuration:  
b2         INFO           
b2         INFO             - atomic                   : building  
b2         INFO             - chrono                   : building  
b2         INFO             - container                : not building  
b2         INFO             - context                  : building  
b2         INFO             - contract                 : not building  
b2         INFO             - coroutine                : building  
b2         INFO             - date_time                : building  
b2         INFO             - exception                : not building  
b2         INFO             - fiber                    : not building  
b2         INFO             - filesystem               : building  
b2         INFO             - graph                    : not building  
b2         INFO             - graph_parallel           : not building  
b2         INFO             - headers                  : not building  
b2         INFO             - iostreams                : building  
b2         INFO             - json                     : not building  
b2         INFO             - locale                   : not building  
b2         INFO             - log                      : building  
b2         INFO             - math                     : not building  
b2         INFO             - mpi                      : not building  
b2         INFO             - nowide                   : not building  
b2         INFO             - program_options          : building  
b2         INFO             - python                   : not building  
b2         INFO             - random                   : building  
b2         INFO             - regex                    : building  
b2         INFO             - serialization            : building  
b2         INFO             - stacktrace               : not building  
b2         INFO             - system                   : building  
b2         INFO             - test                     : building  
b2         INFO             - thread                   : building  
b2         INFO             - timer                    : not building  
b2         INFO             - type_erasure             : not building  
b2         INFO             - url                      : not building  
b2         INFO             - wave                     : not building  
```  
  
```  
b2.exe' -q -d2 -d+4 -j28 '--prefix=d:\build\winarm64_vc140\boost\install' '--build-dir=d:\build\winarm64_vc140' '--user-config=d:\build\ob\sb-67536084\cayman_boost\vmware-config\vmware-config.msvc.jam' --debug-configuration --debug-building --layout=tagged --without-stacktrace --without-exception --without-fiber --without-container --without-headers --without-locale --without-graph --without-contract --without-type_erasure --without-timer --without-mpi --without-python --without-json --without-graph_parallel --without-url --without-nowide --without-wave --without-math address-model=64 debug-symbols=on threading=multi define=BOOST_DISABLE_ASSERTS=1 define=VMWARE_BOOST_CONFIG_DISABLE_AUTO_LINK architecture=arm binary-format=pe abi=aapcs --disable-icu --hash asynch-exceptions=on -sZLIB_BINARY=zlib '-sZLIB_INCLUDE=d:/build/windows2016-clean\winarm64_vc140\include' '-sZLIB_LIBPATH=d:/build/windows2016-clean\winarm64_vc140\lib' 'linkflags=/guard:ehcont /guard:cf' 'cflags=/guard:ehcont /Qspectre /guard:cf' 'cxxflags=/guard:ehcont /Qspectre /guard:cf' link=shared runtime-link=shared release install  
```

---

## Comment 1

> Username: bansalaj  
> Created at: 2023-09-07 04:39:09 UTC  
> Url: https://github.com/boostorg/context/issues/241#issuecomment-1709464383  

in build logs i can see the following output  
  
```  
b2         INFO         Command string for CreateProcessA(): 'cmd.exe /Q/C "d:\build\ob\sb-67536084\tmp\jam6140-10-00.bat"'  
  
b2         INFO         msvc.link.dll d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\boost_context-mt-a64.dll  
  
b2         INFO           
  
b2         INFO                 call "d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\standalone\msvc\97ae7926c5652c1ea4af0a78a6da942c\msvc-setup.bat" x86_arm64 >nul  
  
b2         INFO          link /NOLOGO /INCREMENTAL:NO "d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\asm\make_arm64_aapcs_pe_armasm.obj" "d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\asm\jump_arm64_aapcs_pe_armasm.obj" "d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\asm\ontop_arm64_aapcs_pe_armasm.obj" "d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\windows\stack_traits.obj"      /DEBUG /guard:ehcont /guard:cf /MACHINE:ARM64 /MANIFEST:EMBED /OPT:REF,ICF /subsystem:console /out:"d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\boost_context-mt-a64.dll"   /DLL /IMPLIB:"d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\boost_context-mt-a64.lib"  
  
b2         INFO           
  
b2         INFO            Creating library d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\boost_context-mt-a64.lib and object d:\build\ob\sb-67536084\cayman_boost\build\bet\winarm64_vc140\boost\bin.v2\libs\context\build\d62971186a848aba51a860c6ac8a9a8c\boost_context-mt-a64.exp  
  
b2         INFO         0.000000 sec system; 0.000000 sec user; 0.711430 sec clock  
```

---

## Comment 2

> Username: olk  
> Created at: 2023-10-01 14:52:43 UTC  
> Url: https://github.com/boostorg/context/issues/241#issuecomment-1742105761  

sorry - I don't know, I don't have  system running Windows on ARM64

---

## Comment 3

> Username: bansalaj  
> Created at: 2023-10-01 15:29:49 UTC  
> Url: https://github.com/boostorg/context/issues/241#issuecomment-1742115919  

> sorry - I don't know, I don't have system running Windows on ARM64  
  
the issue could be reproduce when building on x64 machine.  
b2.exe is built using x64. And then B2 is used for cross-compiling boost libraries to architecture=arm. (As MSVC supports cross-compiling)  
The building/compiling is actually happening on x64 machine.

---

## Comment 4

> Username: olk  
> Created at: 2023-10-01 19:13:58 UTC  
> Url: https://github.com/boostorg/context/issues/241#issuecomment-1742172755  

I even don't own a Windows system!

---

## Comment 5

> Username: bansalaj  
> Created at: 2023-10-06 04:53:23 UTC  
> Url: https://github.com/boostorg/context/issues/241#issuecomment-1749982005  

Is there any other group where i can post the issue?  
i did some digging into it, issues is only with context lib  
in file fcontext.hpp  
```  
extern "C" BOOST_CONTEXT_DECL  
transfer_t BOOST_CONTEXT_CALLDECL jump_fcontext( fcontext_t const to, void * vp);  
extern "C" BOOST_CONTEXT_DECL  
fcontext_t BOOST_CONTEXT_CALLDECL make_fcontext( void * sp, std::size_t size, void (* fn)( transfer_t) );  
```  
i'm assuming BOOST_CONTEXT_CALLDECL is setting to __cdecl in case of arm when doing cross-compiling on windows x64 and hence the linker error

---

## Comment 6

> Username: dixyes  
> Created at: 2024-02-18 04:02:17 UTC  
> Url: https://github.com/boostorg/context/issues/241#issuecomment-1950946843  

> Is there any other group where i can post the issue? i did some digging into it, issues is only with context lib in file fcontext.hpp  
>   
> ```  
> extern "C" BOOST_CONTEXT_DECL  
> transfer_t BOOST_CONTEXT_CALLDECL jump_fcontext( fcontext_t const to, void * vp);  
> extern "C" BOOST_CONTEXT_DECL  
> fcontext_t BOOST_CONTEXT_CALLDECL make_fcontext( void * sp, std::size_t size, void (* fn)( transfer_t) );  
> ```  
>   
> i'm assuming BOOST_CONTEXT_CALLDECL is setting to __cdecl in case of arm when doing cross-compiling on windows x64 and hence the linker error  
  
Have you tried specify `abi=aapcs` ?
