# #9 adds missing explicit conversion to bool of optional [Merged]

> Username: gnzlbg  
> Created at: 2014-07-31 18:13:01 UTC  
> Updated at: 2014-07-31 18:16:44 UTC  
> Merged at: 2014-07-31 18:16:44 UTC  
> Closed at: 2014-07-31 18:16:44 UTC  
> Url: https://github.com/boostorg/mpi/pull/9  

Missing explicit conversion to bool caused Boost.MPI to fail to compile  
with C++ > 11.  
  
Fixes [trac #10282](https://svn.boost.org/trac/boost/ticket/10282).

---
