# #60 - Coroutine does not build cleanly on OS-X in universal mode (arm+x86) [Closed]

> Username: clusty  
> Created at: 2022-09-13 15:13:46 UTC  
> Updated at: 2024-08-28 11:06:09 UTC  
> Closed at: 2024-08-28 11:06:09 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60  

I checked out the 1.80 release branch of boost and ran the following commands:  
./bootstrap.sh  
./b2 architecture="arm+x86" cxxflags="-arch x86_64 -arch arm64"  
  
everything seems to have compiled correctly apart from an error at the end:  
...updating 4 targets...  
clang-darwin.link.dll bin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden/libboost_coroutine.dylib  
Undefined symbols for architecture arm64:  
  "_jump_fcontext", referenced from:  
      boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, void*) in coroutine_context.o  
  "_make_fcontext", referenced from:  
      boost::coroutines::detail::coroutine_context::coroutine_context(void (*)(boost::context::detail::transfer_t), boost::coroutines::detail::preallocated const&) in coroutine_context.o  
      boost::coroutines::detail::coroutine_context::coroutine_context(void (*)(boost::context::detail::transfer_t), boost::coroutines::detail::preallocated const&) in coroutine_context.o  
ld: symbol(s) not found for architecture arm64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"   -o "bin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden/libboost_coroutine.dylib" -single_module -dynamiclib -install_name "@rpath/libboost_coroutine.dylib" "bin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden/detail/coroutine_context.o" "bin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden/exceptions.o" "bin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden/posix/stack_traits.o" "bin.v2/libs/context/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden/libboost_context.dylib"         -fPIC -fvisibility=hidden -fvisibility-inlines-hidden -arch arm64 -arch x86_64  
  
...failed clang-darwin.link.dll bin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden/libboost_coroutine.dylib...  
...skipped <p/opt/UnitySrc/git/com.unity.recorder/Native/External/boost/stage/lib>libboost_coroutine.dylib for lack of <pbin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden>libboost_coroutine.dylib...  
...skipped <pbin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden>libboost_coroutine-variant-shared.cmake for lack of <pbin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden>libboost_coroutine.dylib...  
...skipped <p/opt/UnitySrc/git/com.unity.recorder/Native/External/boost/stage/lib/cmake/boost_coroutine-1.80.0>libboost_coroutine-variant-shared.cmake for lack of <pbin.v2/libs/coroutine/build/clang-darwin-13/release/architecture-arm+x86/threading-multi/visibility-hidden>libboost_coroutine-variant-shared.cmake...  
...failed updating 1 target...  
...skipped 3 targets...

---

## Comment 1

> Username: olk  
> Created at: 2022-09-14 16:44:54 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1247038030  

Sorry - I've no access to an machine with OS-X...

---

## Comment 2

> Username: clusty  
> Created at: 2022-09-14 17:46:18 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1247105168  

> Sorry - I've no access to an machine with OS-X...  
  
Is there a way I can help you with that ? :)   
I do have an OSX, but do not know much about the boost build system or coroutine libs

---

## Comment 3

> Username: olk  
> Created at: 2022-10-04 05:17:53 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1266409427  

The arm64 assembler tool doesn't find symbol _jump_fcontext which is the label/function name of jump functionality.  
I don't know the requirements for labels in the universal mode on OS-X.

---

## Comment 4

> Username: olk  
> Created at: 2022-10-04 05:22:45 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1266412125  

relates to https://github.com/boostorg/context/issues/203

---

## Comment 5

> Username: kaamui  
> Created at: 2023-02-03 11:59:54 UTC  
> Updated at: 2023-02-03 12:49:07 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1415770632  

faced the same issue using macports to build boost 1.81.0 [(issue)](https://trac.macports.org/ticket/66820) on a mac M1 (arm)  
  
I would like to help too, as boost is required on other universal packages like poppler and I need it^^.  Any chance we can fix it simply or does it imply big changes ? A location where we can send you a mac M1 😄 ?   
  
Don't know if it can help, but a similar issue was reported some years ago for i386 architecture => https://lists.boost.org/boost-bugs/2014/03/35459.php  
  
What do you think of the "[removal of] Untested.cpp from libs\context\build\Jamfile.v2" as a workaround ? Is it reliable ?  
  
edit : Sounds like the related commits that were pushed at the time to fix the issue on i386 were [these ones](https://github.com/boostorg/context/compare/5460e4603514768ff7c7d131337ffb13b461bc14...fb32c14ad78d608e6ab0ab07a473a5c6b723e1ca#diff-322788b77c72dedcc9b4a12bf0233ceb8451b4a6baa6d851173ecd5320ac14ae)  
  
edit2 : other informations that could help you can be found in [this issue reported on macports](https://trac.macports.org/ticket/66686), where a third symbol was undefined (`_ontop_fcontext`). Also, this issue was reported with boost171, so maybe it was working before.

---

## Comment 6

> Username: olk  
> Created at: 2023-02-03 14:15:41 UTC  
> Updated at: 2023-02-03 14:19:19 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1415930455  

Sorry, I need to debug the code but unfortunately I have no OSX ...  
  
maybe this issue of boost.context https://github.com/boostorg/context/issues/214 fixes your problem?

---

## Comment 7

> Username: kaamui  
> Created at: 2023-02-07 13:38:30 UTC  
> Updated at: 2023-02-07 13:42:03 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1420789080  

No, it didn't.   
  
But I found this [SO thread](https://stackoverflow.com/questions/64553398/compile-boost-as-universal-library-intel-and-apple-silicon-architectures) and the following script worked :   
  
``` bash  
#!/bin/sh  
  
rm -rf arm64 x86_64 universal stage bin.v2  
rm -f b2 project-config*  
./bootstrap.sh cxxflags="-arch x86_64 -arch arm64" cflags="-arch x86_64 -arch arm64" linkflags="-arch x86_64 -arch arm64"  
./b2 toolset=clang-darwin target-os=darwin architecture=arm abi=aapcs cxxflags="-arch arm64" cflags="-arch arm64" linkflags="-arch arm64" -a  
mkdir -p arm64 && cp stage/lib/*.dylib arm64  
./b2 toolset=clang-darwin target-os=darwin architecture=x86 cxxflags="-arch x86_64" cflags="-arch x86_64" linkflags="-arch x86_64" abi=sysv binary-format=mach-o -a  
mkdir x86_64 && cp stage/lib/*.dylib x86_64  
mkdir universal  
for dylib in arm64/*; do   
  lipo -create -arch arm64 $dylib -arch x86_64 x86_64/$(basename $dylib) -output universal/$(basename $dylib);   
done  
for dylib in universal/*; do  
  lipo $dylib -info;  
done  
```  
I'll try to dig more into it, to find if a specific option is making it working for my config, but I suspect that the default options make it impossible to find the correct flags (abi, archs, linkflags) when passing `arm+x86`, so it sounds more like a configuration issue than a technical/low-level issue with symbols, etc  
  
Note that I didn't need to apply the patch you suggested to make it work... maybe because the patch is targeting `sysv` abi where an equivalent patch is needed when compilation is launched from an ARM-based machine ? Something like :   
  
```  
# COMBINED  
alias asm_sources  
   : asm/make_combined_sysv_macho_gas.S  
     asm/jump_combined_sysv_macho_gas.S  
     asm/ontop_combined_sysv_macho_gas.S  
   : <abi>sysv  
     <architecture>arm+x86  
     <binary-format>mach-o  
   ;  
  
alias asm_sources  
   : asm/make_combined_sysv_macho_gas.S  
     asm/jump_combined_sysv_macho_gas.S  
     asm/ontop_combined_sysv_macho_gas.S  
   : <abi>aapcs  
     <address-model>64  
     <architecture>arm+x86  
     <binary-format>mach-o  
   ;  
```  
  
Maybe ?

---

## Comment 8

> Username: Kojoley  
> Created at: 2023-07-06 22:55:11 UTC  
> Url: https://github.com/boostorg/coroutine/issues/60#issuecomment-1624407061  

https://github.com/boostorg/context/pull/228 should've fixed this (if `clang -arch x86_64 -arch arm64 asm.S` is actually able to produce an universal binary).
