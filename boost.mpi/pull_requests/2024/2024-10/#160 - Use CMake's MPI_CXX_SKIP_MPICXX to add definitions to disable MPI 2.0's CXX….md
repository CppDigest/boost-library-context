# #160 Use CMake's MPI_CXX_SKIP_MPICXX to add definitions to disable MPI 2.0's CXX… [Merged]

> Username: LXYan2333  
> Created at: 2024-10-14 14:39:11 UTC  
> Updated at: 2024-10-29 23:26:00 UTC  
> Merged at: 2024-10-29 14:42:34 UTC  
> Closed at: 2024-10-29 14:42:34 UTC  
> Url: https://github.com/boostorg/mpi/pull/160  

# Original problem  
  
Currently, boost_mpi links to `MPI::MPI_C` target and use   
  
```cpp  
/* Force MPICH not to define SEEK_SET, SEEK_CUR, and SEEK_END, which  
   conflict with the versions in <stdio.h> and <cstdio>. */  
#define MPICH_IGNORE_CXX_SEEK 1  
/* We do not want to link in the OpenMPI CXX stuff */  
#define OMPI_SKIP_MPICXX  
```  
  
to disable OpenMPI and MPICH's deprecated MPICXX binding. However, if user write code like this:  
  
```cpp  
#include <mpi.h>  
#include <boost/mpi.hpp>  
```  
  
these macro won't be able to disable the MPI's CXX binding, and symbols leaks to the translation units, cause really weired link error in my machine:  
  
```console  
undefined references to `MPI::Comm::Comm()'  
```  
  
# Fix  
  
After reading the CMake's [FindMPI documentation](https://cmake.org/cmake/help/latest/module/FindMPI.html) and reading the `FindMPI.cmake` (especially [this line](https://github.com/Kitware/CMake/blob/82dafe874ec9a38c443bb1517fd58eb2c7c82a7e/Modules/FindMPI.cmake#L1118C52-L1118C69)) I believe the correct way to link a CPP binary object to MPI's C library is:  
  
```cmake  
set(MPI_CXX_SKIP_MPICXX ON)  
find_package(MPI REQUIRED COMPONENTS CXX)  
target_link_libraries(boost_mpi PUBLIC MPI::MPI_CXX)  
```  
  
In this way, CMake will add `MPICH_SKIP_MPICXX`, `OMPI_SKIP_MPICXX` and `_MPICC_H` definitions so cxx binding headers can be reliably and portably disabled.

---

## Comment 1

> Username: LXYan2333  
> Created_at: 2024-10-16 04:43:43 UTC  
> Url: https://github.com/boostorg/mpi/pull/160#issuecomment-2415720066  

Maybe a similar line `set(MPI_CXX_SKIP_MPICXX ON)` should also prepend to  
  
https://github.com/boostorg/cmake/blob/17e920c84fb232c320232c79941244ea3da67477/include/BoostInstall.cmake#L386

---

## Comment 2

> Username: aminiussi  
> Created_at: 2024-10-29 14:49:54 UTC  
> Updated_at: 2024-10-29 14:50:21 UTC  
> Url: https://github.com/boostorg/mpi/pull/160#issuecomment-2444492543  

I merged the resquest. Regarding the `MPI_CXX_SKIP_MPICXX` that should prepend           
```  
 string(APPEND CONFIG_FILE_CONTENTS "find_dependency(MPI COMPONENTS CXX)\n")  
 string(APPEND CONFIG_FILE_CONTENTS "set(MPI_CXX_SKIP_MPICXX ON)\n")  
```  
I do not have permision to  add that.  
  
But I guess that can be proposed.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-10-29 15:53:36 UTC  
> Url: https://github.com/boostorg/mpi/pull/160#issuecomment-2444686443  

The general idea is fine, but I don't think the definitions should be removed from `boost/mpi/config.hpp`, because Boost.MPI is not exclusively used from CMake.

---

## Comment 4

> Username: aminiussi  
> Created_at: 2024-10-29 16:08:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/160#issuecomment-2444727365  

> The general idea is fine, but I don't think the definitions should be removed from `boost/mpi/config.hpp`, because Boost.MPI is not exclusively used from CMake.  
  
Right. I going to roll back that one, but add some guard to avoid double definition warning

---

## Comment 5

> Username: aminiussi  
> Created_at: 2024-10-29 16:19:01 UTC  
> Url: https://github.com/boostorg/mpi/pull/160#issuecomment-2444755224  

Done

---

## Comment 6

> Username: LXYan2333  
> Created_at: 2024-10-29 23:26:00 UTC  
> Url: https://github.com/boostorg/mpi/pull/160#issuecomment-2445505652  

> Right. I going to roll back that one, but add some guard to avoid double definition warning  
  
I read that commit, but I think the correct macro for mpich is `MPICH_SKIP_MPICXX`, and there is also a `_MPICC_H` include guard for IBM platform MPI:  
  
on my machine: `/usr/include/x86_64-linux-gnu/mpich/mpi.h`  
  
```c  
#if defined(__cplusplus)  
}  
/* Add the C++ bindings */  
/*   
   If MPICH_SKIP_MPICXX is defined, the mpicxx.h file will *not* be included.  
   This is necessary, for example, when building the C++ interfaces.  It  
   can also be used when you want to use a C++ compiler to compile C code,  
   and do not want to load the C++ bindings.  These definitions can  
   be made by the C++ compilation script  
 */  
#if !defined(MPICH_SKIP_MPICXX)  
/* mpicxx.h contains the MPI C++ binding.  In the mpi.h.in file, this   
   include is in an autoconf variable in case the compiler is a C++   
   compiler but MPI was built without the C++ bindings */  
#include "mpicxx.h"  
#endif   
#endif  
```  
  
on FindMPI.cmake:  
  
```cmake  
    if(MPI_CXX_SKIP_MPICXX AND NOT MPI_${LANG}_COMPILE_DEFINITIONS MATCHES "SKIP_MPICXX")  
      # MPICH_SKIP_MPICXX is being used in MPICH and derivatives like MVAPICH or Intel MPI  
      # OMPI_SKIP_MPICXX is being used in Open MPI  
      # _MPICC_H is being used for IBM Platform MPI  
      list(APPEND MPI_${LANG}_COMPILE_DEFINITIONS "MPICH_SKIP_MPICXX" "OMPI_SKIP_MPICXX" "_MPICC_H")  
      set(MPI_${LANG}_COMPILE_DEFINITIONS "${MPI_${LANG}_COMPILE_DEFINITIONS}" CACHE STRING "MPI ${LANG} compilation definitions" FORCE)  
    endif()  
```

---
