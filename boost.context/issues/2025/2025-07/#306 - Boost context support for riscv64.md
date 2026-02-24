# #306 - Boost context support for riscv64 [Closed]

> Username: vinodh0808  
> Created at: 2025-07-10 10:06:14 UTC  
> Updated at: 2025-07-15 10:44:33 UTC  
> Closed at: 2025-07-15 10:44:33 UTC  
> Url: https://github.com/boostorg/context/issues/306  

Hi,  
  
We are trying to build the Boost library using the Conan recipe from conan-center-index for the RISC-V architecture. However, we are encountering build errors related to the Boost.Context library. The error message indicates missing symbols such as make_fcontext, jump_fcontext, and ontop_fcontext during the linking stage.  
  
  
```  
/usr/lib/gcc-cross/riscv64-linux-gnu/14/../../../../riscv64-linux-gnu/bin/ld: /home/vcn1cob/.conan2/p/b/boost6a1f060a014d9/p/lib/libboost_context.so: undefined reference to `ontop_fcontext'  
/usr/lib/gcc-cross/riscv64-linux-gnu/14/../../../../riscv64-linux-gnu/bin/ld: /home/vcn1cob/.conan2/p/b/boost6a1f060a014d9/p/lib/libboost_context.so: undefined reference to `jump_fcontext'  
/usr/lib/gcc-cross/riscv64-linux-gnu/14/../../../../riscv64-linux-gnu/bin/ld: /home/vcn1cob/.conan2/p/b/boost6a1f060a014d9/p/lib/libboost_context.so: undefined reference to `make_fcontext'  
[ 90%] Built target lambda_exe  
collect2: error: ld returned 1 exit status  
gmake[2]: *** [CMakeFiles/coroutine_exe.dir/build.make:104: coroutine_exe] Error 1  
gmake[1]: *** [CMakeFiles/Makefile2:1152: CMakeFiles/coroutine_exe.dir/all] Error 2  
gmake[1]: *** Waiting for unfinished jobs....  
[ 93%] Linking CXX executable fiber_exe  
/usr/lib/gcc-cross/riscv64-linux-gnu/14/../../../../riscv64-linux-gnu/bin/ld: /home/vcn1cob/.conan2/p/b/boost6a1f060a014d9/p/lib/libboost_context.so: undefined reference to `ontop_fcontext'  
/usr/lib/gcc-cross/riscv64-linux-gnu/14/../../../../riscv64-linux-gnu/bin/ld: /home/vcn1cob/.conan2/p/b/boost6a1f060a014d9/p/lib/libboost_context.so: undefined reference to `jump_fcontext'  
/usr/lib/gcc-cross/riscv64-linux-gnu/14/../../../../riscv64-linux-gnu/bin/ld: /home/vcn1cob/.conan2/p/b/boost6a1f060a014d9/p/lib/libboost_context.so: undefined reference to `make_fcontext'  
collect2: error: ld returned 1 exit status  
gmake[2]: *** [CMakeFiles/fiber_exe.dir/build.make:105: fiber_exe] Error 1  
gmake[1]: *** [CMakeFiles/Makefile2:1216: CMakeFiles/fiber_exe.dir/all] Error 2  
[ 96%] Linking CXX executable process_exe  
[100%] Linking CXX executable regex_exe  
[100%] Built target process_exe  
[100%] Built target regex_exe  
gmake: *** [Makefile:101: all] Error 2  
```  
  
We do not have much experience with Boost internals, so we are unsure how to proceed. Could someone from the Boost team provide guidance or support on how to resolve this issue? Is RISC-V currently supported by Boost.Context, or are there any known workarounds or plans for future support?  
  
Any help or pointers would be greatly appreciated.  
  
build log:   
  
[build.log](https://github.com/user-attachments/files/21160560/build.log)

---

## Comment 1

> Username: vinodh0808  
> Created at: 2025-07-10 10:12:24 UTC  
> Url: https://github.com/boostorg/context/issues/306#issuecomment-3056737163  

@olk

---

## Comment 2

> Username: vinodh0808  
> Created at: 2025-07-15 08:26:36 UTC  
> Updated at: 2025-07-15 09:45:29 UTC  
> Url: https://github.com/boostorg/context/issues/306#issuecomment-3072646864  

I guess, this issue I mentioned (refer build.log attached) is similar to the issue here: [https://github.com/boostorg/context/issues/243](https://github.com/boostorg/context/issues/243)  
  
`error: No best alternative for libs/context/build/asm_sources with <abi>sysv <address-model>32 <architecture>riscv <asynch-exceptions>off <binary-format>elf <boost.locale.iconv.lib>libc <boost.locale.iconv>on <boost.locale.icu>off <context-impl>fcontext <coverage>off <cxxstd-dialect>gnu <cxxstd>2a <debug-symbols>on <deduced-address-model>64 <deduced-architecture>riscv <exception-handling>on <extern-c-nothrow>off <inlining>off <link>shared <numa>on <optimization>off <os>LINUX <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <python>3.13 <relevant>abi <relevant>address-model <relevant>architecture <relevant>binary-format <relevant>toolset <rtti>on <runtime-debugging>on <runtime-link>shared <stdlib>native <strip>off <target-os>linux <testing.execute>on <threadapi>pthread <threading>multi <toolset-gcc:version>14 <toolset>gcc <variant>debug <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on...`  
  
I can see this issue still present in 1.87.0  
  
Any idea whether this is already fixed in boost library? @kxxt

---

## Comment 3

> Username: vinodh0808  
> Created at: 2025-07-15 10:44:33 UTC  
> Url: https://github.com/boostorg/context/issues/306#issuecomment-3073114763  

Issue is obsolete, I have configuration issues and solved.
