# #57 - Boost log compilation error [Closed]

> Username: DeepthyR  
> Created at: 2018-07-16 09:37:15 UTC  
> Updated at: 2018-07-16 09:55:40 UTC  
> Closed at: 2018-07-16 09:55:40 UTC  
> Url: https://github.com/boostorg/log/issues/57  

Hi,  
I am trying to compile Boost 1.64.0 using android NDK 16b for arm64 using gcc (since we have specific requirement for gcc compilation)  
It fails to compile boost_log and results in below error .  
  
boost/boost_1_64_0/libs/log/src/dump_avx2.cpp:21:23: fatal error: immintrin.h: No such file or directory  
#include <immintrin.h>  
compilation terminated.  
CMakeFiles/boost_log.dir/build.make:278: recipe for target 'CMakeFiles/boost_log.dir/boost_1_64_0/libs/log/src/dump_avx2.cpp.o' failed  
make[2]: *** [CMakeFiles/boost_log.dir/boost_1_64_0/libs/log/src/dump_avx2.cpp.o] Error 1  
make[2]: *** Waiting for unfinished  
  
My build environment:  
Android NDK - android-ndk16b  
NDK API level - 24  
C Compiler - GCC (GNU 4.9.0)  
Cmake - 3.9.4  
STL - gnustl_static  
Host - Ubuntu 16.04.4  
  
Please help me in resolving this. Thank you.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-07-16 09:55:40 UTC  
> Url: https://github.com/boostorg/log/issues/57#issuecomment-405198970  

Boost.Log does not support cmake and builds with Boost.Build. If you want to use cmake you will have to reproduce pre-build checks that Boost.Build scripts perform. The scripts test if projects in libs/log/config are able to compile.  
  
The particular error you're getting is because you're compiling sources specific to x86 architecture. You need to exclude dump_ssse3.cpp and dump_avx2.cpp from compilation and *not* define `BOOST_LOG_USE_SSSE3` and `BOOST_LOG_USE_AVX2`.
