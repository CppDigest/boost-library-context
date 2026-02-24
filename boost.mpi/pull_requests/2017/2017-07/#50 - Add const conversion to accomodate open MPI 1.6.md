# #50 Add const conversion to accomodate open MPI 1.6 [Merged]

> Username: aminiussi  
> Created at: 2017-07-24 11:39:22 UTC  
> Updated at: 2017-07-24 11:39:40 UTC  
> Merged at: 2017-07-24 11:39:40 UTC  
> Closed at: 2017-07-24 11:39:40 UTC  
> Url: https://github.com/boostorg/mpi/pull/50  

Some (old) MPI implementation have a non conforming signature w.r.t.  
This is the case for **MPI_Send**, those signature should be, according to the [standard](http://mpi-forum.org/docs/mpi-3.1/mpi31-report.pdf#56):  
{{{  
int MPI_Send(const void* buf, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm)  
}}}

---
