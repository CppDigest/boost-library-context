# #90 - Power8 Not Recognized as BOOST_OS_LINUX [Closed]

> Username: ax3l  
> Created at: 2018-11-23 14:17:07 UTC  
> Updated at: 2018-11-26 08:59:35 UTC  
> Closed at: 2018-11-23 14:25:49 UTC  
> Url: https://github.com/boostorg/predef/issues/90  

Boost.Predef does not recognize OpenPOWER 8 systems as `BOOST_OS_LINUX` properly, although they are running a Linux.  
  
## System Info  
  
[D.A.V.I.D.E.](https://wiki.u-gov.it/confluence/display/SCAIUS/UG3.2%3A+D.A.V.I.D.E.+UserGuide) cluster, running with GCC 6.4.0 on one of its head nodes:  
  
```  
$ lsb_release -a  
LSB Version:	:core-4.1-noarch:core-4.1-ppc64le  
Distributor ID:	CentOS  
Description:	CentOS Linux release 7.5.1804 (AltArch)   
Release:	7.5.1804  
Codename:	AltArch  
  
$ uname -a (on head node)  
Linux davidefe02 3.10.0-862.3.2.el7.ppc64le #1 SMP Tue May 22 03:45:03 BST 2018 ppc64le ppc64le ppc64le GNU/Linux  
```  
  
### Software Info  
  
GCC 6.4.0  
NVCC 9.2.88  
Boost 1.68.0  
  
### Tests  
  
```C++  
#include <boost/predef.h>  
#include <iostream>  
  
int main()  
{  
#if defined(linux)  
    std::cout << "linux" << std::endl;  
#endif  
  
#if defined(_linux)  
    std::cout << "_linux" << std::endl;  
#endif  
  
#if defined(__linux)  
    std::cout << "__linux" << std::endl;  
#endif  
  
#if defined(BOOST_OS_UNIX)  
    std::cout << "BOOST: UNIX" << std::endl;  
#endif  
  
#if defined(BOOST_OS_LINUX)  
    std::cout << "BOOST: LINUX" << std::endl;  
#else  
    std::cout << "BOOST: not a LINUX" << std::endl;  
#endif  
  
    return 0;  
}  
```  
  
`g++ main.cpp` runs as:  
```  
linux  
__linux  
BOOST: UNIX  
BOOST: LINUX  
```  
  
`nvcc main.cu` (9.2.88) runs as:  
```  
BOOST: UNIX  
BOOST: LINUX  
```  
  
but `nvcc` does not define those macros in the device trajectory.  
  
-> this is an `nvcc` issue on power only, reported now as Nvidia bug id 2448610

---

## Comment 1

> Username: grafikrobot  
> Created at: 2018-11-23 16:53:45 UTC  
> Url: https://github.com/boostorg/predef/issues/90#issuecomment-441285715  

Did you resolve the problem? Is there a problem?

---

## Comment 2

> Username: ax3l  
> Created at: 2018-11-26 08:58:09 UTC  
> Updated at: 2018-11-26 08:59:35 UTC  
> Url: https://github.com/boostorg/predef/issues/90#issuecomment-441563755  

The problem is with Nvidia's NVCC compiler, reported now as Nvidia bug id 2448610. I am waiting for upstream feedback. The CUDA SDK, which ships nvcc, supports the [`ppc64le` architecture on Linux](https://developer.nvidia.com/cuda-downloads?target_os=Linux) only, but does not yet set `-Dlinux`/`-D__linux` macros as for CUDA 9.2 (and probably current CUDA 10.0 as well).  
  
As a downstream project work-around, I just passed `-Dlinux` on those platforms based on CMake logic until `nvcc` fixes it:  
```cmake  
                # nvcc sets no linux/__linux macros on OpenPOWER linux  
                # nvidia bug id: 2448610  
                IF(CMAKE_SYSTEM_NAME STREQUAL "Linux")  
                    IF(CMAKE_SYSTEM_PROCESSOR STREQUAL "ppc64le")  
                        LIST(APPEND CUDA_NVCC_FLAGS "-Dlinux")  
                    ENDIF()  
                ENDIF()  
```
