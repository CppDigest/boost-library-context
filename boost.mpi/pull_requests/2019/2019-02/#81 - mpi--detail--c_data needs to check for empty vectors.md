# #81 mpi::detail::c_data needs to check for empty vectors [Merged]

> Username: mkuron  
> Created at: 2019-02-14 14:56:49 UTC  
> Updated at: 2019-03-11 13:13:10 UTC  
> Merged at: 2019-03-11 13:13:10 UTC  
> Closed at: 2019-03-11 13:13:10 UTC  
> Url: https://github.com/boostorg/mpi/pull/81  

If the standard library is configured to do range checks (`-D_GLIBCXX_ASSERTIONS`), accessing the zeroth element of a vector to get its address triggers an assertion.  
  
This issue was discovered in https://github.com/espressomd/espresso/issues/2507, where `boost::mpi::detail::dispatch_scatter_sendbuf` runs into it.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2019-03-11 11:01:29 UTC  
> Url: https://github.com/boostorg/mpi/pull/81#issuecomment-471495150  

I'll merge this in develop soon. Should be in 1.71  
Thanks

---
