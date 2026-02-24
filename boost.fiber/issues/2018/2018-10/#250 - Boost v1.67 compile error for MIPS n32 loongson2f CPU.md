# #250 - Boost v1.67 compile error for MIPS n32 loongson2f CPU [Closed]

> Username: emtone  
> Created at: 2018-10-28 05:33:06 UTC  
> Updated at: 2022-09-18 14:38:33 UTC  
> Closed at: 2022-09-18 14:38:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/250  

I use gentoo on Lemote Yeeloong 8101 notebook which has a MIPS n32 loongson2f CPU. It's OK when I compile without "context" USE flag. Now I want to use KiCad on this computer which need boost recompile with "contest" USE flag:  
`[ebuild     U *] dev-libs/boost-1.67.0:0/1.67.0::gentoo [1.65.0:0/1.65.0::gentoo] USE="context* nls python threads -debug -doc -icu (-mpi) -static-libs -tools" ABI_MIPS="(n32) -n64 -o32" PYTHON_TARGETS="python2_7 python3_6 -python3_4 -python3_5 -python3_7%" 0 KiB`  
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

> Username: mclow  
> Created at: 2018-11-05 01:48:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/250#issuecomment-435731616  

this looks like a Boost.Fiber bug, and should be reported there.  
https://github.com/boostorg/fiber

---

## Comment 2

> Username: emtone  
> Created at: 2018-11-05 14:57:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/250#issuecomment-435904630  

Ok, thank you mclow, I will reported this at there.

---

## Comment 3

> Username: emtone  
> Created at: 2018-11-05 16:19:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/250#issuecomment-435935904  

I add some loongson2f predef at here, is this right?  
```  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/boost/predef/architecture # pwd  
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/boost/predef/architecture  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/boost/predef/architecture # ls  
alpha.h  arm.h  blackfin.h  convex.h  ia64.h  m68k.h  mips.h  parisc.h  ppc.h  pyramid.h  rs6k.h  sparc.h  superh.h  sys370.h  sys390.h  x86  x86.h  z.h  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/boost/predef/architecture # cat mips.h  
/*  
Copyright Rene Rivera 2008-2015  
Distributed under the Boost Software License, Version 1.0.  
(See accompanying file LICENSE_1_0.txt or copy at  
http://www.boost.org/LICENSE_1_0.txt)  
*/  
  
#ifndef BOOST_PREDEF_ARCHITECTURE_MIPS_H  
#define BOOST_PREDEF_ARCHITECTURE_MIPS_H  
  
#include <boost/predef/version_number.h>  
#include <boost/predef/make.h>  
  
/*`  
[heading `BOOST_ARCH_MIPS`]  
  
[@http://en.wikipedia.org/wiki/MIPS_architecture MIPS] architecture.  
  
[table  
    [[__predef_symbol__] [__predef_version__]]  
  
    [[`__mips__`] [__predef_detection__]]  
    [[`__mips`] [__predef_detection__]]  
    [[`__MIPS__`] [__predef_detection__]]  
  
    [[`__mips`] [V.0.0]]  
    [[`_MIPS_ISA_MIPS1`] [1.0.0]]  
    [[`_R3000`] [1.0.0]]  
    [[`_MIPS_ISA_MIPS2`] [2.0.0]]  
    [[`__MIPS_ISA2__`] [2.0.0]]  
    [[`_R4000`] [2.0.0]]  
    [[`_MIPS_ISA_MIPS3`] [3.0.0]]  
    [[`__MIPS_ISA3__`] [3.0.0]]  
    [[`_LOONGSON2F`] [3.0.0]]  
    [[`_MIPS_ISA_MIPS4`] [4.0.0]]  
    [[`__MIPS_ISA4__`] [4.0.0]]  
    ]  
 */  
  
#define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
  
#if defined(__mips__) || defined(__mips) || \  
    defined(__MIPS__)  
#   undef BOOST_ARCH_MIPS  
#   if !defined(BOOST_ARCH_MIPS) && (defined(__mips))  
#       define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER(__mips,0,0)  
#   endif  
#   if !defined(BOOST_ARCH_MIPS) && (defined(_MIPS_ISA_MIPS1) || defined(_R3000))  
#       define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER(1,0,0)  
#   endif  
#   if !defined(BOOST_ARCH_MIPS) && (defined(_MIPS_ISA_MIPS2) || defined(__MIPS_ISA2__) || defined(_R4000))  
#       define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER(2,0,0)  
#   endif  
#   if !defined(BOOST_ARCH_MIPS) && (defined(_MIPS_ISA_MIPS3) || defined(__MIPS_ISA3__) || defined(_LOONGSON2F))  
#       define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER(3,0,0)  
#   endif  
#   if !defined(BOOST_ARCH_MIPS) && (defined(_MIPS_ISA_MIPS4) || defined(__MIPS_ISA4__))  
#       define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER(4,0,0)  
#   endif  
#   if !defined(BOOST_ARCH_MIPS)  
#       define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER_AVAILABLE  
#   endif  
#endif  
  
#if BOOST_ARCH_MIPS  
#   define BOOST_ARCH_MIPS_AVAILABLE  
#endif  
  
#define BOOST_ARCH_MIPS_NAME "MIPS"  
  
#endif  
  
#include <boost/predef/detail/test.h>  
BOOST_PREDEF_DECLARE_TEST(BOOST_ARCH_MIPS,BOOST_ARCH_MIPS_NAME)  
  
```
