# #453 - [bug] QNX SDP 7.0 debug build with stabs [Closed]

> Username: Johnnyxy  
> Created at: 2019-06-21 09:39:54 UTC  
> Updated at: 2024-03-08 19:18:14 UTC  
> Closed at: 2024-03-08 19:18:13 UTC  
> Url: https://github.com/boostorg/build/issues/453  

Hi there,  
while compiling code with QNX SDP 7.0 (GCC 5.4) I get an error:  
>qcc.compile.c++ C:\boost\bin.v2\libs\thread\build\qcc-5.4\dbg\lnk-sttc\trgt-os-qnxnt\thrdp-pthrd\vsblt-hdn\future.o  
  
>    "QCC" -lang-c++ -Wc,-ftemplate-depth-128 -fvisibility-inlines-hidden -Vgcc_ntoaarch64le_cxx -fPIC -gstabs+ -O0 -Wc,-fno-inline -Wc,-Wall -fvisibility=hidden -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX -I"." -c -o "C:\boost\bin.v2\libs\thread\build\qcc-5.4\dbg\lnk-sttc\trgt-os-qnxnt\thrdp-pthrd\vsblt-hdn\future.o" "libs\thread\src\future.cpp"  
  
>libs\thread\src\future.cpp:1:0: error: target system does not support the 'stabs' debug format  
 // (C) Copyright 2012 Vicente J. Botet Escriba  
 ^  
cc: c:/qnx700//host/win64/x86_64/usr/lib/gcc/aarch64-unknown-nto-qnx7.0.0/5.4.0/cc1plus caught signal 1  
...failed qcc.compile.c++ C:\boost\bin.v2\libs\thread\build\qcc-5.4\dbg\lnk-sttc\trgt-os-qnxnt\thrdp-pthrd\vsblt-hdn\future.o...  
  
I investigated the problem and found out that the ```qcc.jam``` specifies the ```-gstabs+``` flag.  
https://github.com/boostorg/build/blob/a83f94aad28b0096dbe5d870e1c8f158adf0c922/src/tools/qcc.jam#L51  
https://github.com/boostorg/build/blob/a83f94aad28b0096dbe5d870e1c8f158adf0c922/src/tools/qcc.jam#L167  
  
The QCC compiler wrapper docs describe only ```-g``` as compiler argument (see [QNX 7.0](http://www.qnx.com/developers/docs/7.0.0/#com.qnx.doc.neutrino.utilities/topic/q/qcc.html), [QNX 6.5](http://www.qnx.com/developers/docs/6.5.0/index.jsp?topic=%2Fcom.qnx.doc.neutrino_utilities%2Fq%2Fqcc.html)).  
  
  
I have patched the file with the attached patch file (locally):  
[boost_build_qcc_fix_debug_build_parameter.patch.txt](https://github.com/boostorg/build/files/3313862/boost_build_qcc_fix_debug_build_parameter.patch.txt)

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:01 UTC  
> Url: https://github.com/boostorg/build/issues/453#issuecomment-859203320  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2024-03-08 19:18:13 UTC  
> Url: https://github.com/boostorg/build/issues/453#issuecomment-1986265426  

Fixed externally thanks to @johnmcfarlane
