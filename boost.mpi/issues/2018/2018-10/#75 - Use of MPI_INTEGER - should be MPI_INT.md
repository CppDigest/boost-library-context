# #75 - Use of MPI_INTEGER - should be MPI_INT [Closed]

> Username: pmblakely  
> Created at: 2018-10-18 12:05:00 UTC  
> Updated at: 2018-12-09 22:31:03 UTC  
> Closed at: 2018-12-09 22:31:03 UTC  
> Url: https://github.com/boostorg/mpi/issues/75  

There are six uses of MPI_INTEGER in the following three files:  
boost/mpi/collectives/gather.hpp  
boost/mpi/collectives/all_gather.hpp  
boost/mpi/collectives/scatter.hpp  
  
These should be replaced by the C-type MPI_INT, as MPI_INTEGER is a Fortran type only.  
  
I encountered this as a bug when compiling against an installation of OpenMPI v3.1.2 which was compiled without Fortran support. In this case, any use of MPI_INTEGER from a C/C++ program leads to an "invalid datatype" MPI error. Presumably OpenMPI aliases MPI_INTEGER to MPI_INT and all is well, unless MPI_INTEGER is not supported due to lack of Fortran.
