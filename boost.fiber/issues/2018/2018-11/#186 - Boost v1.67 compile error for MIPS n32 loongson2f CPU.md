# #186 - Boost v1.67 compile error for MIPS n32 loongson2f CPU [Closed]

> Username: emtone  
> Created at: 2018-11-05 16:07:11 UTC  
> Updated at: 2018-11-06 05:56:32 UTC  
> Closed at: 2018-11-05 20:45:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/186  

I use gentoo on Lemote Yeeloong 8101 notebook which has a MIPS n32 loongson2f CPU. It's OK when I compile without "context" USE flag. Now I want to use KiCad on this computer which need boost recompile with "contest" USE flag:  
`[ebuild U *] dev-libs/boost-1.67.0:0/1.67.0::gentoo [1.65.0:0/1.65.0::gentoo] USE="context* nls python threads -debug -doc -icu (-mpi) -static-libs -tools" ABI_MIPS="(n32) -n64 -o32" PYTHON_TARGETS="python2_7 python3_6 -python3_4 -python3_5 -python3_7%" 0 KiB`  
When I compile with "context" USE flag, An error has occurred:  
```  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
error: No best alternative for libs/context/build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
```  
```  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam  
    - zlib                     : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - iconv (libc)             : yes  
    - native-atomic-int32-supported : yes  
    - native-syslog-supported  : yes  
    - pthread-supports-robust-mutexes : yes  
    - compiler-supports-visibility : yes  
    - x86                      : no  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - gcc visibility           : yes  
    - long double support      : yes  
    - libbacktrace builds      : no  
    - addr2line builds         : yes  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : yes  
```  
```  
 "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/fiber/build/gcc-8.2/gentoorelease/pch-off/threading-multi/algo/work_stealing.o" "libs/fiber/src/algo/work_stealing.cpp"  
  
{standard input}: Assembler messages:  
{standard input}:1475: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:1545: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:1552: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:2254: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:2327: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
...failed updating 1 target...  
```

---

## Comment 1

> Username: olk  
> Created at: 2018-11-05 20:45:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/186#issuecomment-436028312  

see boost.context #88 ... you have already posted this issue (boost.fiber uses boost.context)

---

## Comment 2

> Username: emtone  
> Created at: 2018-11-06 01:57:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/186#issuecomment-436104218  

Hi olk, I think you referenced issue is not the boost.context issue.

---

## Comment 3

> Username: emtone  
> Created at: 2018-11-06 02:01:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/186#issuecomment-436105049  

"mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/fiber/build/gcc-8.2/gentoorelease/architecture-mips3/pch-off/threading-multi/algo/work_stealing.o" "libs/fiber/src/algo/work_stealing.cpp"  
  
I think there are some thing need fix to support loongson2f CPU in boostorg/fiber.

---

## Comment 4

> Username: olk  
> Created at: 2018-11-06 05:56:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/186#issuecomment-436139747  

Needs to be fixed in boost.context (contains relevant MIPS part)
