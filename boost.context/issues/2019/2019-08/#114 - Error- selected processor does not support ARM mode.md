# #114 - Error: selected processor does not support ARM mode [Closed]

> Username: kwinsch  
> Created at: 2019-08-07 09:22:17 UTC  
> Updated at: 2023-08-08 12:06:47 UTC  
> Closed at: 2019-08-08 16:44:40 UTC  
> Url: https://github.com/boostorg/context/issues/114  

I run into the problem cross-compiling boost/context for ARM Cortex-A5 processors, using the conan package boost/1.70.0@conan/stable . I found a very similar bug report from 5 years ago under: https://svn.boost.org/trac10/ticket/10343 . With that in mind, I tried to pin the error.  
  
The missing assembler commands `vstmia` and `vldmia` are fully supported on my target processor. I analysed the build log and saw, that the boost build system does not include all CFLAGS during compilation of the context submodule. The required assembler commands are only valid in the context of a defined vfp implementation. In my case, the flag -mfpu=neon-vfpv4 MUST be included to sucessfully build the files.  
  
The build system generated the follwing sample command:  
`  
"/home/user/.conan/data/gcc-arm-linux-musleabihf/0.1/annax/testing/package/44fcf6b9a7fb86b2586303e3db40189d3b511830/bin/arm-linux-musleabihf-g++" -x assembler-with-cpp -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_SOURCE -DBOOST_DISABLE_ASSERTS -DNDEBUG -D_GLIBCXX_USE_CXX11_ABI=1 -I"." -c -o "/home/user/.conan/data/boost/1.70.0/conan/stable/build/6d53ac5f4723d1ff68e9cc7a543dd2401412be50/boost/bin.v2/libs/context/build/gcc-8/rls/abi-apcs/lnk-sttc/thrd-mlt/vsblt-hdn/asm/jump_arm_aapcs_elf_gas.o" "libs/context/src/asm/jump_arm_aapcs_elf_gas.S"  
`  
  
Which failed in:  
```  
ontop_arm_aapcs_elf_gas.S: Assembler messages:  
ontop_arm_aapcs_elf_gas.S:57: Error: selected processor does not support `vstmia sp,{d8-d15}' in ARM mode  
ontop_arm_aapcs_elf_gas.S:71: Error: selected processor does not support `vldmia sp,{d8-d15}' in ARM mode  
```  
  
For other boost modules, the build system correctly applies all supplied CFLAGS:  
`  
"/home/user/.conan/data/gcc-arm-linux-musleabihf/0.1/annax/testing/package/44fcf6b9a7fb86b2586303e3db40189d3b511830/bin/arm-linux-musleabihf-g++"   -fvisibility-inlines-hidden -g -O3 -mcpu=cortex-a5 -mfpu=neon-vfpv4 -mfloat-abi=hard -fstack-protector-strong -Wformat -Werror=format-security -flto -funroll-loops -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_STATIC_LINK=1 -DNDEBUG -D_GLIBCXX_USE_CXX11_ABI=1  -I"." -c -o "/home/user/.conan/data/boost/1.70.0/conan/stable/build/6d53ac5f4723d1ff68e9cc7a543dd2401412be50/boost/bin.v2/libs/container/build/gcc-8/rls/lnk-sttc/thrd-mlt/vsblt-hdn/pool_resource.o" "libs/container/src/pool_resource.cpp"  
`  
  
By manually inserting the `-mcpu=cortex-a5 -mfpu=neon-vfpv4 -mfloat-abi=hard`, the error disapears. Unfortunatly, I am not very familar with bjam and b2 to find the bug. I think, if the build of the context module also inherits all the CFLAGS like all other modules do, the build failure would disapear. Corrected command:  
  
`  
"/home/user/.conan/data/gcc-arm-linux-musleabihf/0.1/annax/testing/package/44fcf6b9a7fb86b2586303e3db40189d3b511830/bin/arm-linux-musleabihf-g++"   -fvisibility-inlines-hidden -g -O3 -mcpu=cortex-a5 -mfpu=neon-vfpv4 -mfloat-abi=hard -fstack-protector-strong -Wformat -Werror=format-security -flto -funroll-loops -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -Wextra -Wno-long-long -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_All_STATIC_LINK=1 -DNDEBUG -D_GLIBCXX_USE_CXX11_ABI=1  -I"." -c -o "/home/user/.conan/data/boost/1.70.0/conan/stable/build/6d53ac5f4723d1ff68e9cc7a543dd2401412be50/boost/bin.v2/libs/context/build/gcc-8/rls/abi-apcs/lnk-sttc/thrd-mlt/vsblt-hdn/asm/jump_arm_aapcs_elf_gas.o" "libs/context/src/asm/jump_arm_aapcs_elf_gas.S"  
`  
  
The recipe for the toolchain used, can be found under: https://gist.github.com/kwinsch/596d0b5a7ef72a8140b47a3b556e43d6  
[boost_buildlog.txt](https://github.com/boostorg/context/files/3476228/boost_buildlog.txt)

---

## Comment 1

> Username: kwinsch  
> Created at: 2019-08-07 12:53:41 UTC  
> Url: https://github.com/boostorg/context/issues/114#issuecomment-519083101  

Setting the ASFLAGS to:  
`ASFLAGS = -mcpu=cortex-a5 -mfpu=neon-vfpv4 -mfloat-abi=hard `  
  
solves the problem for me. I anyway feel, that the buildsystem should force the selection or give an adequate warning, if the `-mfpu` option is not set for the arm platform. The context module will never build without it and the abort error gives no hint of the problem.

---

## Comment 2

> Username: olk  
> Created at: 2019-08-08 16:44:39 UTC  
> Url: https://github.com/boostorg/context/issues/114#issuecomment-519597780  

please add a bug report for boost build system

---

## Comment 3

> Username: LaySoft  
> Created at: 2022-02-22 10:56:33 UTC  
> Url: https://github.com/boostorg/context/issues/114#issuecomment-1047673335  

> Setting the ASFLAGS to: `ASFLAGS = -mcpu=cortex-a5 -mfpu=neon-vfpv4 -mfloat-abi=hard `  
>   
> solves the problem for me. I anyway feel, that the buildsystem should force the selection or give an adequate warning, if the `-mfpu` option is not set for the arm platform. The context module will never build without it and the abort error gives no hint of the problem.  
  
I ran into it, when I'd like to install openswoole pecl package (https://pecl.php.net/get/openswoole-4.10.0.tgz) on a Raspberry running on Ubuntu 21.10. Can you please tell me, how can I fix this package with your suggested workaround?  
  
In which boost project should a ticket be opened?

---

## Comment 4

> Username: bo-nemk  
> Created at: 2023-08-08 12:06:47 UTC  
> Url: https://github.com/boostorg/context/issues/114#issuecomment-1669487676  

> Setting the ASFLAGS to: `ASFLAGS = -mcpu=cortex-a5 -mfpu=neon-vfpv4 -mfloat-abi=hard `  
>   
> solves the problem for me. I anyway feel, that the buildsystem should force the selection or give an adequate warning, if the `-mfpu` option is not set for the arm platform. The context module will never build without it and the abort error gives no hint of the problem.  
  
Heads up for others, I built this with the CMake build, I needed to set "ASMBUILD" instead. Based on [this](https://cmake.org/cmake/help/latest/envvar/ASM_DIALECTFLAGS.html).
