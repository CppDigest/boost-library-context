# #67 - Missing MPI::MPI_C dependency in boost::mpi targets [Open]

> Username: Neumann-A  
> Created at: 2024-03-09 10:52:23 UTC  
> Updated at: 2024-03-09 11:02:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/67  

BoostInstall.cmake does simply not check for MPI_C only MPI_CXX.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-03-09 11:02:32 UTC  
> Url: https://github.com/boostorg/boost_install/issues/67#issuecomment-1986825599  

I believe that MPI_CXX is the correct dependency. According to the documentation of FindMPI (https://cmake.org/cmake/help/v3.29/module/FindMPI.html),  
  
> The module exposes the components C, CXX, MPICXX and Fortran. Each of these controls the various MPI languages to search for. The difference between CXX and MPICXX is that CXX refers to the MPI C API being usable from C++, whereas MPICXX refers to the MPI-2 C++ API that was removed again in MPI-3.  
  
Since Boost.MPI is a C++ library and has no C source files, MPI_CXX should be the right target, according to the documentation above.  
  
That said, the CMakeLists.txt file for MPI has been changed to link to MPI_C instead (https://github.com/boostorg/mpi/commit/f2f586ec4a8daa7000fa4ea85e5bc5076c7b7c33). I don't think this change is correct, for the reason given above.
