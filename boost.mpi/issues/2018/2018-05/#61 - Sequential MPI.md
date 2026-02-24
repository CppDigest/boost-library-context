# #61 - Sequential MPI [Open]

> Username: aminiussi  
> Created at: 2018-05-21 14:52:48 UTC  
> Updated at: 2018-05-21 14:52:48 UTC  
> Url: https://github.com/boostorg/mpi/issues/61  

Some code exist in both parallel en sequential form, there are various ways to deal with that, one is the ifdef  all the MPI calls, the other is to provide a dummy/empty MPI implementation.  
  
I think it would be a good idea to provide a sequential version of Boost MPI so that users could have both parallel and sequential versions with no source code modifications.  
  
One consequence is that we could provide that version on all platforms, even those with no MPI implementations, as users might still want the sequential version. Which could even help catching some compilations problems.  
  
I expecting some bjam problems though...
