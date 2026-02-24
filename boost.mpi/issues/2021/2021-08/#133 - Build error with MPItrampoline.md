# #133 - Build error with MPItrampoline [Closed]

> Username: eschnett  
> Created at: 2021-08-18 18:20:14 UTC  
> Updated at: 2023-03-14 09:48:30 UTC  
> Closed at: 2023-03-14 09:48:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/133  

I want to build Boost.MPI with [MPItrampoline](https://github.com/eschnett/MPItrampoline]), an MPI implementation that merely forwards MPI calls to another MPI implementation. This MPI implementation stores most MPI constants in global variables that are initialized at startup, i.e. they are not compile-time constants. I believe the MPI standard (e.g. section 2.5.4 in the standard for MPI 3.1) allows this explicitly.  
  
Boost.MPI's definition of the enum `threading::level` leads to compile-time errors; e.g. `single = MPI_THREAD_SINGLE` is then not valid for an enum constant.
