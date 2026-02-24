# #148 - Deactivating context library with b2 doesn't prevent building libcontext [Closed]

> Username: kheaactua  
> Created at: 2020-07-31 15:11:58 UTC  
> Updated at: 2020-07-31 16:05:35 UTC  
> Closed at: 2020-07-31 16:05:35 UTC  
> Url: https://github.com/boostorg/context/issues/148  

In my build output, I see  
  
```  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : not building  
    - coroutine                : building  
    ...  
```  
  
However, `context` is still built.  I can see by the errors I'm getting (separate issue):  
  
```  
clang-linux.compile.asm /home/developer/.conan/data/boost/1.64.0/conan/stable/build/a34395d58ab18823f0870551152ea88ec13e520c/boost/bin.v2/libs/context/build/clng-lnx-9.0/rls/abi-apcs/lnk-sttc/trgt-os-andrd/thrd-mlt/asm/make_arm64_aapcs_elf_gas.o  
  
    "/opt/android-sdk/ndk/21.3.6528147/toolchains/llvm/prebuilt/linux-x86_64/bin/clang++" -x assembler-with-cpp -O3 -Wno-inline -Wall -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_SOURCE -DBOOST_DISABLE_ASSERTS -DNDEBUG -I"." -c -o "/home/developer/.conan/data/bo  
ost/1.64.0/conan/stable/build/a34395d58ab18823f0870551152ea88ec13e520c/boost/bin.v2/libs/context/build/clng-lnx-9.0/rls/abi-apcs/lnk-sttc/trgt-os-andrd/thrd-mlt/asm/make_arm64_aapcs_elf_gas.o" "libs/context/src/asm/make_arm64_aapcs_elf_gas.S"  
  
libs/context/src/asm/make_arm64_aapcs_elf_gas.S:60:5: error: unknown use of instruction mnemonic without a size suffix  
    and x0, x0, ~0xF  
    ^  
...  
```  
  
I see this from 1.64 (at least) to the 1.72

---

## Comment 1

> Username: olk  
> Created at: 2020-07-31 16:05:34 UTC  
> Url: https://github.com/boostorg/context/issues/148#issuecomment-667201213  

boost.fiber, boost.coroutine and boost.coroutine2 depend on boost.context - you have to deactivate then too.
