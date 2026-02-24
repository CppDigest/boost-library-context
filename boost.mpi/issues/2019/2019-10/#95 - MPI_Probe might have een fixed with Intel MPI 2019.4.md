# #95 - MPI_Probe might have een fixed with Intel MPI 2019.4 [Closed]

> Username: aminiussi  
> Created at: 2019-10-22 09:43:38 UTC  
> Updated at: 2020-11-27 15:16:03 UTC  
> Closed at: 2019-10-22 10:01:56 UTC  
> Url: https://github.com/boostorg/mpi/issues/95  

Meaning that #63 and #62 might behave correctly with that implementation

---

## Comment 1

> Username: aminiussi  
> Created at: 2019-10-22 10:04:19 UTC  
> Url: https://github.com/boostorg/mpi/issues/95#issuecomment-544890948  

Merged on develop and master.  
Tested on openmpi 2.1.1 and Intel MPI 2019.4

---

## Comment 2

> Username: mennodeij  
> Created at: 2020-11-27 15:16:03 UTC  
> Url: https://github.com/boostorg/mpi/issues/95#issuecomment-734881076  

Today I found out that Intel MPI 2020.1 (to be exact: `icc (ICC) 19.1.1.217 20200306`) still has problems with MPI_IMProbe. If I remove any bits that `#define BOOST_MPI_USE_IMPROBE 1` from `boost/mpi/config.hpp` the problem we experienced with this goes away.  
  
It was difficult to pinpoint but it looks like calls to `mpi::wait_all` did not get handled properly, but I can't be sure.
