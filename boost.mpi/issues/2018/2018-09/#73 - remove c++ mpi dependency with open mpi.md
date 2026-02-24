# #73 - remove c++ mpi dependency with open mpi [Closed]

> Username: aminiussi  
> Created at: 2018-09-19 13:39:15 UTC  
> Updated at: 2019-10-27 20:54:00 UTC  
> Closed at: 2019-10-27 20:54:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/73  

We do not used the C++ binding, but OpenMPI forces them on us (and then we foce them on the end user).  
This can be avoided by setting the OMPI_SKIP_MPICXX definition in the config file.
