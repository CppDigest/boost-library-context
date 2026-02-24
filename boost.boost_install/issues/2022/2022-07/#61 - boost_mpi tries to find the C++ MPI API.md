# #61 - boost_mpi tries to find the C++ MPI API [Open]

> Username: aminiussi  
> Created at: 2022-07-13 15:33:40 UTC  
> Updated at: 2022-07-13 18:56:36 UTC  
> Url: https://github.com/boostorg/boost_install/issues/61  

boost_mpi is only based on the C MPI API. Yet the the generated cmake code tries to import the C++ API, bringing in more stuff than necessary. Along with the associated problems:  
  
```  
CMake Error in /ccc/work/cont003/gen7233/miniussa/views/fargo/fargOCA/icc/rel/CMakeFiles/CMakeTmp/CMakeLists.txt:  
  Imported target "MPI::MPI_CXX" includes non-existent path  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-07-13 18:56:36 UTC  
> Url: https://github.com/boostorg/boost_install/issues/61#issuecomment-1183569715  

MPI_CXX is not the C++ API. See https://cmake.org/cmake/help/latest/module/FindMPI.html:  
  
> The difference between CXX and MPICXX is that CXX refers to the MPI C API being usable from C++, whereas MPICXX refers to the MPI-2 C++ API that was removed again in MPI-3.
