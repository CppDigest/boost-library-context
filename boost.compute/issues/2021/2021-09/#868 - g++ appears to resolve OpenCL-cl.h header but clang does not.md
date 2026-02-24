# #868 - g++ appears to resolve OpenCL/cl.h header but clang does not. [Open]

> Username: teamiceberg  
> Created at: 2021-09-08 00:26:06 UTC  
> Updated at: 2021-09-08 00:26:06 UTC  
> Url: https://github.com/boostorg/compute/issues/868  

The compile output of the two files on a simple boost.compute example program (compute/example/list_devices.cpp) on Mac OSX 11.5.2 is listed below:  
  
_[files also have the elapsed times of the various compiler and linker stages]_  
  
[gpp_compile.txt](https://github.com/boostorg/compute/files/7124889/gpp_compile.txt)  
[clang_compile.txt](https://github.com/boostorg/compute/files/7124890/clang_compile.txt)  
  
As you can see the clang compiler appears to have produced a **fatal** error:  
  
_"In file included from /Users/shivamacpro/Desktop/EducationandProjects/boost-compute-library/compute/include/boost/compute/config.hpp:16:  
/Users/shivamacpro/Desktop/EducationandProjects/boost-compute-library/compute/include/boost/compute/cl.hpp:17:10: fatal error: 'OpenCL/cl.h' file not found  
#include <OpenCL/cl.h>"_  
  
The output of list_devices on my Macbook pro when successfully compiled under g++ is:  
  
Platform 'Apple'  CPU Device: Intel(R) Core(TM) i7-7920HQ CPU @ 3.10GHz  
  GPU Device: Intel(R) HD Graphics 630  
  GPU Device: AMD Radeon Pro 560 Compute Engine  
  
Any idea what might be happening here? Or if this is a bug, should we work toward fixing it?
