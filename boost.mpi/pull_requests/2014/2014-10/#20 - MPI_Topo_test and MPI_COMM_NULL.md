# #20 MPI_Topo_test and MPI_COMM_NULL [Merged]

> Username: aminiussi  
> Created at: 2014-10-06 17:02:47 UTC  
> Updated at: 2014-10-07 08:13:41 UTC  
> Merged at: 2014-10-07 08:13:37 UTC  
> Closed at: 2014-10-07 08:13:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/20  

MPI_COMM_NULL is not a valid argument for MPI_Topo_test.   
But it is used to indicated leftover processes when creating graph communicator or cartesians grids (for exampl, when you create a 2X2 grid from a 5 process communicator).  
  
So we need to deal with it as a special case.

---
