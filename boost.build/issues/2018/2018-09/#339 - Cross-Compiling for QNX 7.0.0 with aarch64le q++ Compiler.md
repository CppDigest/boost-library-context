# #339 - Cross-Compiling for QNX 7.0.0 with aarch64le q++ Compiler [Closed]

> Username: kirillschwez  
> Created at: 2018-09-10 13:29:28 UTC  
> Updated at: 2019-06-12 15:25:17 UTC  
> Closed at: 2018-09-10 18:19:44 UTC  
> Url: https://github.com/boostorg/build/issues/339  

Hello!   
  
I'm cross-compiling Boost v1.68.0 for QNX 7.0.0  
I have modified the qcc.jam to support the q++ (C++ Compiler in QNX 7.0.0)  
  
  
rule init ( version ? : command * : options * )  
{  
    local condition = [ common.check-init-parameters qcc : version $(version) ] ;  
    local command = [ common.get-invocation-command qcc : **q++** : $(command) ] ;  
    common.handle-options qcc : $(condition) : $(command) : $(options) ;  
}  
  
and I added -Vgcc_ntoaarch64le flag to ar  
actions piecemeal archive  
{  
    $(RM) "$(<)"  
    "$(CONFIG_COMMAND)" **-Vgcc_ntoaarch64le** -A "$(<)" "$(>)"  
}  
  
  
Build command line  
  
./b2 -q install link=shared toolset=qcc target-os=qnxnto variant=release cxxflags="-Vgcc_ntoaarch64le -Y_gpp" --without-context --without-coroutine --without-python --prefix=${QNX_TARGET}/usr  
  
  
But I get complier error  
  
qcc.link.dll bin.v2/libs/container/build/qcc/release/libboost_container.so.1.68.0  
/srv/workspace/msm8996au-hqx-0-1_hlos_dev.git/qnx_ap/qnx_bins/prebuilt_QNX700/host/linux/x86_64/usr/bin/aarch64-unknown-nto-qnx7.0.0-ld: bin.v2/libs/container/build/qcc/release/**alloc_lib.o: Relocations in generic ELF (EM: 3)**  
/srv/workspace/msm8996au-hqx-0-1_hlos_dev.git/qnx_ap/qnx_bins/prebuilt_QNX700/host/linux/x86_64/usr/bin/aarch64-unknown-nto-qnx7.0.0-ld: bin.v2/libs/container/build/qcc/release/**alloc_lib.o: Relocations in generic ELF (EM: 3)**  
**bin.v2/libs/container/build/qcc/release/alloc_lib.o: error adding symbols: File in wrong format**  
cc: /srv/workspace/msm8996au-hqx-0-1_hlos_dev.git/qnx_ap/qnx_bins/prebuilt_QNX700/host/linux/x86_64/usr/bin/aarch64-unknown-nto-qnx7.0.0-ld error 1  
  
    "q++" -Vgcc_ntoaarch64le   -o "bin.v2/libs/container/build/qcc/release/libboost_container.so.1.68.0" -Wl,-h -Wl,libboost_container.so.1.68.0 -shared "bin.v2/libs/container/build/qcc/release/alloc_lib.o" "bin.v2/libs/container/build/qcc/release/dlmalloc.o" "bin.v2/libs/container/build/qcc/release/global_resource.o" "bin.v2/libs/container/build/qcc/release/monotonic_buffer_resource.o" "bin.v2/libs/container/build/qcc/release/pool_resource.o" "bin.v2/libs/container/build/qcc/release/synchronized_pool_resource.o" "bin.v2/libs/container/build/qcc/release/unsynchronized_pool_resource.o"    -lm   
  
...failed qcc.link.dll bin.v2/libs/container/build/qcc/release/libboost_container.so.1.68.0...  
...failed updating 1 target...  
  
  
I compile boost for with -shared option.  
Error does not happen with static variant.  
  
Can ypu please advise, what can be the problem?  
Many thanks!

---

## Comment 1

> Username: kirillschwez  
> Created at: 2018-09-10 18:19:44 UTC  
> Updated at: 2018-09-10 18:20:40 UTC  
> Url: https://github.com/boostorg/build/issues/339#issuecomment-420010868  

The Problem was solved by adding **cflag** to the c compiler **-Vgcc_ntoaarch64le**  
because alloc_lib.c was compiled by qcc for the x86 platform (default) and not for arm64.

---

## Comment 2

> Username: Johnnyxy  
> Created at: 2019-06-12 15:25:16 UTC  
> Url: https://github.com/boostorg/build/issues/339#issuecomment-501324605  

Hi there,  
I have to compile for QNX 7.0 too.  
Anyway I just what remark here that it is not advised to modify the recipe for a specific architecture ```aarch64le``` in your case, as this will not be nessecary to build Boost for a specific target.
