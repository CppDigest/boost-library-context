# #315 - Why boost.context export make_x86_64_sysv_elf_gas.o [Open]

> Username: code-share1  
> Created at: 2018-06-06 06:27:28 UTC  
> Updated at: 2021-06-26 03:10:07 UTC  
> Url: https://github.com/boostorg/build/issues/315  

when build context library, it will compile some assembly files. After the context library generated, I use readelf to analyze the context library as below:   
readelf --wide --symbols /my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/libboost_context.so.1.67.0  
  
42: 0000000000000000 0 FILE LOCAL DEFAULT ABS /my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/asm/make_x86_64_sysv_elf_gas.o  
  
Why does the make_x86_64_sysv_elf_gas.o line exist in the output of "readelf --wide --symbols /my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/libboost_context.so.1.67.0", is there any means to hidden the line?  
  
  
BTW, my compile command as below:  
"x86_64-poky-linux-g++" "-m64" "-march=core2" "-mtune=core2" "-msse3" "-mfpmath=sse" "-Wl,-O1" "-Wl,--hash-style=gnu" "-Wl,--as-needed" "-f    stack-protector-strong" "-Wl,-z,relro,-z,now" "-fstack-protector-strong" "-D_FORTIFY_SOURCE=2" "--sysroot=/my-build/boost/1.67.0-r0/recipe-sysroot"    -o "/my-build/boost/1.67.0-r0/boost_1_67_ 0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/libboost_context.so.1.67.0" -Wl,-h -Wl,libboost_context.so.1.67.0 -shared -Wl,--start-group "/my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/asm/make_x86_64_sysv_elf_gas.o" "/my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/asm/jump_x86_64_sysv_elf_gas.o" "/my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/asm/ontop_x86_64_sysv_elf_gas.o" "/my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425f6c3a38ed/execution_context.o" "/my-build/boost/1.67.0-r0/boost_1_67_0/x86_64-poky-linux/boost/bin.v2/libs/context/build/aca09349fdb84d131321425   f6c3a38ed/posix/stack_traits.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -Wl,-O1 -Wl,--hash-style=gnu -Wl,--a    s-needed -fstack-protector-strong -Wl,-z,relro,-z,now

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:27 UTC  
> Url: https://github.com/boostorg/build/issues/315#issuecomment-868936451  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
