# #414 Update clang-vxworks.jam and gcc.jam for VxWorks [Closed]

> Username: kuhlenough  
> Created at: 2019-03-19 20:32:03 UTC  
> Updated at: 2021-10-02 21:13:34 UTC  
> Closed at: 2020-01-18 00:36:04 UTC  
> Url: https://github.com/boostorg/build/pull/414  

This upstreams what Wind River is shipping with Boost 1.68 support for VxWorks.  
It's  tested with about dozen configurations of clang 7.0 and gcc 8.1.  IA and ARM processor boards use clang; PowerPC boards use gcc.  
  
The fancy bit I really hope doesn't break any other platform is the added generators to link with **ld** instead of the compiler driver. This is to align with what we are doing with VxWorks generally.  
  
------------------------------------------------------------------  
For the record those trying with earlier versions of VxWorks,  since I will get emails about this, you can't just run **b2** and pray it works, we capture the boards VSB build environment in a shell file e.g:  
   ```  
#!/bin/bash   
# This is a generated file. Do not edit.  
# Contents set the cross build environment for the VxWorks Source Build project:  
# /yow-build62-lx2/bkuhl/workspace/VSB            
 export CPU='SIMLINUX'   
 export TOOL='llvm'   
 export VSB_DIR='/yow-build62-lx2/bkuhl/workspace/VSB'   
 export ROOT_DIR='/yow-build62-lx2/bkuhl/workspace/VSB/usr/root'   
 export CC='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/clang'   
 export CPP='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/clang -E'   
 export CXX='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/clang'   
 export AR='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/arpentium'   
 export AS='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/aspentium'   
 export NM='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/nmpentium'   
 export READELF='readelfpentium'   
 export STRIP='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/strippentium -g'   
 export RPATH='/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/objboost/common/PIC'   
 export RRPATH='/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/PIC'   
 export SHLIB_LD=''   
 export LDFLAGS_SO='-shared -L/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/objboost/common/PIC'   
 export LIBPATH='/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/objboost/common'   
 export LIBROOTPATH='/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common'   
 export CCSHARED='-fpic -D__SO_PICABILINUX__ -D__SO64__LARGE__ -ftls-model=global-dynamic -mcmodel=small'   
 export CRT0_OBJ='/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common//crt0.o'   
 export WIND_BASE='/yow-build62-lx2/bkuhl/vx7/helix/guests/vxworks-7'   
 export LDFLAGS_SHARED='--exclude-libs libc_internal.a -u __init -u __fini -u __wr_need_frame_add'   
 export LDFLAGS_SHARED_EXTRA='-lc_internal -T/yow-build62-lx2/bkuhl/workspace/VSB/usr/ldscripts/rtp.ld'   
 export LINKFLAGS='--defsym __wrs_rtp_base=0x200000000 -u __wr_need_frame_add -u __tls__ -T/yow-build62-lx2/bkuhl/workspace/VSB/usr/ldscripts/rtp.ld   -z common-page-size=8192'   
 export LINKFLAGS_SO='-shared -L/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/PIC'   
 export CFLAGS='--target=x86_64 -m64 -mcmodel=large -fno-omit-frame-pointer -mno-red-zone -fno-strict-aliasing -mno-implicit-float -fno-builtin -nostdlibinc -nostdinc++ -ftls-model=local-exec -std=c11 -fasm -O2 -Wuninitialized -Wimplicit -Wreturn-type -Warray-bounds -Wformat -Wno-c++11-narrowing'        
 export CPPFLAGS=' -include/yow-build62-lx2/bkuhl/workspace/VSB/3pp/BOOST/boost_1_68_0/autoconf_vsbConfig_quote.h -D__vxworks -D__VXWORKS__ -D__RTP__ -D__ELF__ -D_USE_INIT_ARRAY -D__WRS_NO_DIRECT_TLS_ACCESS_FROM_RTP_TO_SHARED_LIB -I/yow-build62-lx2/bkuhl/workspace/VSB/3pp/BOOST/boost_1_68_0 @/yow-build62-lx2/bkuhl/workspace/VSB/mk/_vsbuinc_cmdllvmBOOST -D_VX_CPU=_VX_SIMLINUX -D_VX_TOOL_FAMILY=llvm -D_VX_TOOL=llvm -D_nq_VSB_CONFIG_FILE=/yow-build62-lx2/bkuhl/workspace/VSB/h/config/vsbConfig.h -I/yow-build62-lx2/bkuhl/workspace/VSB/share/h -isystem/yow-build62-lx2/bkuhl/workspace/VSB/usr/h -isystem/yow-build62-lx2/bkuhl/workspace/VSB/usr/h/system -isystem/yow-build62-lx2/bkuhl/workspace/VSB/usr/h/public -D_VSB_PUBLIC_HDR_DIR=/yow-build62-lx2/bkuhl/workspace/VSB/usr/h/public -I.'      
 export LD='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/ldpentium -m elf_x86_64'            
 export LDSHARED='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/ldpentium -m elf_x86_64 -shared -L/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/objboost/common/PIC'      
 export LDCXXSHARED='/yow-build62-lx2/bkuhl/vx7/compilers/llvm-7.0.0.1/LINUX64/bin/ldpentium -m elf_x86_64 -shared -L/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/objboost/common/PIC'   
 export LDFLAGS='--defsym __wrs_rtp_base=0x200000000 -u __wr_need_frame_add -u __tls__ -T/yow-build62-lx2/bkuhl/workspace/VSB/usr/ldscripts/rtp.ld   -L/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common/objboost/common -z common-page-size=8192'       
 export CXXFLAGS='--target=x86_64 -m64 -mcmodel=large -fno-omit-frame-pointer -mno-red-zone -fno-strict-aliasing -mno-implicit-float -fno-builtin -nostdlibinc -nostdinc++ -ftls-model=local-exec -ansi -std=c++14 -O2 -Wall -Wconversion -Wno-sign-conversion -Wno-missing-braces -Wno-c++11-narrowing'      
 export LIBS='--start-group -lunix -lnet  -lc  -lc_internal  -lllvm  -lcplusplus  -lllvmcplus  -ldl --end-group'          
 export LIBS_BARE='-lunix -lnet  -lc  -lc_internal  -lllvm  -lcplusplus  -lllvmcplus  -ldl'     
 export SHLIBS='-lunix -lnet -lc -lc_internal -lllvm -lcplusplus -lllvmcplus'        
 export LDFLAGS_STATIC='-static'   
 export LDFLAGS_DYNAMIC='--as-needed -lunix -lnet -lc -lc_internal -lllvm -lcplusplus -lllvmcplus'  
 export LDFLAGS_EXE='/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common//crt0.o'  
 export LINKFORSHARED='--as-needed'  
  ```  
  
Which then get parsed into the jam environment through a big **project-config.jam**  
  
```  
# Boost.Build Configuration  
#  
# Copyright 2016, 2018 Wind River Systems, Inc.  
#  
# Use, modification and distribution are subject to the  
# Boost Software License, Version 1.0.  (See accompanying file  
# LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
#  
#  modification history  
#  --------------------  
#  14dec18,dlk  compensate for changes in rules.env.sh.mk  
#  28nov18,b_c  skip the tests which compile with no C++ exceptions  
#   9sep18,brk  kernel build support  
#   4sep18,brk  update for new llvm compiler  
#  31mar16,brk  written  
#  
  
import option ;  
import feature ;  
import os ;  
import path ;  
import type ;  
  
# <link>static still builds some shared library tests  
# the project requirments below ensure that this does not  
# happen with <static-only>on  
# The <static-only>kernel is functionaly identically to <static-only>on,  
# it's just a mechanism to store kernel objects in a unique path  
  
feature.feature static-only  
    : on off kernel  
    : composite propagated optional ;  
  
feature.compose <static-only>on  
    : <link>static ;  
  
feature.compose <static-only>kernel  
    : <link>static ;  
  
# consolidating all build features into a single  
# composite feature reduces the build subdirectories to a minimum  
# and reduces the chance of exceeding the maximum path length in Windows  
  
feature.feature cross-compile  
    : vxworks  
    : composite propagated optional ;  
  
feature.compose <cross-compile>vxworks  
  : <target-os>vxworks  
    <threadapi>pthread  
    <threading>multi  
    <inlining>full  
    <optimization>speed  
    <binary-format>elf  
    <abi>sysv  
  ;  
  
  
# source cross build environment  
  
cflags = [ os.environ CFLAGS ] ;  
cxxflags = [ os.environ CXXFLAGS ] ;  
compileflags = [ os.environ CPPFLAGS ] ;  
compileflags-so = [ os.environ CCSHARED ] ;  
linkflags = [ os.environ LINKFLAGS ] ;  
linkflags-so = [ os.environ LINKFLAGS_SO ] ;  
linkflags-shared = [ os.environ LDFLAGS_SHARED ] ;  
linkflags-shared-extra = [ os.environ LDFLAGS_SHARED_EXTRA ] ;  
linkflags-static = [ os.environ LDFLAGS_STATIC ] ;  
linkflags-dynamic = [ os.environ LINKFORSHARED ] ;  
library-file = [ os.environ LIBS_BARE ] ;  
library-path = [ os.environ LIBROOTPATH ] ;  
dll-path = [ os.environ RRPATH ] ;  
cc = [ os.environ CC ] ;  
cpp = [ os.environ CPP ] ;  
cxx = [ os.environ CXX ] ;  
ar = [ os.environ AR ] ;  
as = [ os.environ AS ] ;  
linker = [ os.environ LD ] ;  
crt0-obj = [ os.environ CRT0_OBJ ] ;  
  
using gcc : : $(cxx) :  
                    <compileflags>$(compileflags)  
                    <cflags>$(cflags)  
                    <cxxflags>$(cxxflags)  
                    <linkflags>$(linkflags)  
  
                    <archiver>$(ar)  
                    <linker>$(linker)  
                    <linkflags-so>"$(linkflags-so) $(linkflags-shared) $(linkflags-shared-extra)"  
                    <compileflags-so>$(compileflags-so)  
                    <linkflags-static>$(linkflags-static)  
                    <linkflags-dynamic>$(linkflags-dynamic)  
                    <crt0-obj>$(crt0-obj)  
                    <library-path>$(library-path)  
                    <library-file>$(library-file) ;  
  
using clang-vxworks : : $(cxx) :  
                    <compileflags>$(compileflags)  
                    <cflags>$(cflags)  
                    <cxxflags>$(cxxflags)  
                    <linkflags>$(linkflags)  
  
                    <archiver>$(ar)  
                    <linker>$(linker)  
                    <linkflags-so>$(linkflags-so)  
                    <compileflags-so>$(compileflags-so)  
                    <linkflags-static>$(linkflags-static)  
                    <linkflags-dynamic>$(linkflags-dynamic)  
                    <crt0-obj>$(crt0-obj)  
                    <library-path>$(library-path)  
                    <library-file>$(library-file) ;  
  
type.set-generated-target-suffix EXE :  
			<target-os>vxworks <static-only>kernel : "out" ;  
  
project : requirements  
          <cross-compile>vxworks  
          <static-only>on,<link>shared:<build>no  
          <static-only>kernel,<link>shared:<build>no  
          <exception-handling>off:<build>no  
#        : default-build  
          ;  
  
# Python configuration  
import python ;  
if ! [ python.configured ]  
{  
	local wind-home = [ os.environ WIND_HOME ] ;  
	local wind-host = [ os.environ WIND_HOST_TYPE ] ;  
    using python : 2.7  
    : [ path.native [ path.join $(wind-home) workbench-4 $(wind-host) bin/wrpython27 ] ]  
    : # includes  
    : [ path.native [ path.join $(wind-home) workbench-4  $(wind-host) lib/python ] ]  
    : # <target-os>vxworks  
    : ;  
}  
  
# Stop on first error  
option.set keep-going : false ;  
```  
  
When we do invoke **b2** it's with a lot of baggage,   
```  
./b2 install -j72 --prefix=/yow-build62-lx2/bkuhl/workspace/VSB/usr/root --libdir=/yow-build62-lx2/bkuhl/workspace/VSB/usr/lib/common --includedir=/yow-build62-lx2/bkuhl/workspace/VSB/usr/h/public \  
	   static-only=on toolset=clang-vxworks  --reconfigure address-model=64 \  
	   --with-atomic --with-chrono --with-context --with-exception --with-filesystem --with-log --with-math --with-system -q -d2  
```

---

## Comment 1

> Username: kuhlenough  
> Created_at: 2019-03-25 03:13:48 UTC  
> Url: https://github.com/boostorg/build/pull/414#issuecomment-476040664  

Could someone decipher the automated build failures for me, they make no sense to me?  They seem to be checking a vxworks build with Windows version of clang?

---

## Comment 2

> Username: swatanabe  
> Created_at: 2019-03-25 03:25:09 UTC  
> Url: https://github.com/boostorg/build/pull/414#issuecomment-476042383  

AMDG  
  
On 3/24/19 9:13 PM, Brian Kuhl wrote:  
> Could someone decipher the automated build failures for me, they make no sense to me?  They seem to be checking a vxworks build with Windows version of clang?  
>   
  
It's not using clang at all.  It's a python script that  
compares the given command line arguments against a  
hard-coded list of expected arguments.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: kuhlenough  
> Created_at: 2019-03-29 15:42:54 UTC  
> Updated_at: 2019-03-29 15:43:30 UTC  
> Url: https://github.com/boostorg/build/pull/414#issuecomment-478046118  

@swatanabe can you point me to the test code so I can modify the pull to pass, or recommend a change to make it compatible with the this update?   The current clang-vxworks.jam is very out of date at this point, the only thing it really worked with was a POC version, not the Clang 6 & 7 VxWorks shipped with in recent releases.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2019-04-01 20:13:09 UTC  
> Url: https://github.com/boostorg/build/pull/414#issuecomment-478729323  

AMDG  
  
On 3/29/19 9:42 AM, Brian Kuhl wrote:  
> @swatanabe can you point me to the test code so I can modify the pull to pass, or recommend a change to make it compatible with the this update?   The current clang-vxworks.jam is very out of date at this point, the only thing it really worked with was a POC version, the Clang 6 & 7 VxWorks shipped with in recent releases.   
>   
  
The test cases are in test/toolset-mock/src  
They can be run with test/toolset_*.py  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: kuhlenough  
> Created_at: 2020-01-18 00:36:04 UTC  
> Url: https://github.com/boostorg/build/pull/414#issuecomment-575845864  

@swatanabe I'm closing this pull.  I'm working on simpler version for the new SDK.  ( https://labs.windriver.com/vxworks-sdk/#read ) that has new driver for the compiler, so all the extra generators for linker are no longer required,  the compiler can be used for linking.   However I still having problems with mock toolchain validation,  I don't understand syntax of python code, and the error message doesn't seem to tell what's missing.  Can you give me a bit more detail?

---
