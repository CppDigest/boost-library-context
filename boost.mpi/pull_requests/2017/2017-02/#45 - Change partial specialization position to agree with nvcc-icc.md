# #45 Change partial specialization position to agree with nvcc/icc [Merged]

> Username: aminiussi  
> Created at: 2017-02-23 12:51:35 UTC  
> Updated at: 2017-02-23 17:03:36 UTC  
> Merged at: 2017-02-23 12:56:21 UTC  
> Closed at: 2017-02-23 12:56:21 UTC  
> Url: https://github.com/boostorg/mpi/pull/45  

Change partial specialization position to agree with nvcc/icc.  
Similar to #44 by @gnthibault and #41 by @pkestene.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2017-02-23 12:52:25 UTC  
> Url: https://github.com/boostorg/mpi/pull/45#issuecomment-281983923  

pass on intel15, checking on g++ 5.2.0 before merging.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2017-02-23 12:55:28 UTC  
> Url: https://github.com/boostorg/mpi/pull/45#issuecomment-281984549  

Passed on g++ 5.2.0

---

## Comment 3

> Username: gnthibault  
> Created_at: 2017-02-23 14:06:27 UTC  
> Updated_at: 2017-02-23 14:24:26 UTC  
> Url: https://github.com/boostorg/mpi/pull/45#issuecomment-281999875  

Thank you @aminiussi for the work. Just one question however, I am not familiar with boost testing environment.  
  
When I run b2 inside boost/libs/mpi/test, I just get the following output :  
  
...found 1 target...  
  
Is there something I am doing wrong ?  
  
PS: ouch now nvcc is crashing on boost::mpi::allocator related includes:  
boost/boost/mpi/allocator.hpp(136): internal error: assertion failed at: "/dvs/p4/build/sw/rel/gpu_drv/r361/r361_00/drivers/compiler/edg/EDG_4.10/src/types.c"  
  
I may have to report this problem to nvidia developers in the next official release of boost

---

## Comment 4

> Username: aminiussi  
> Created_at: 2017-02-23 17:03:36 UTC  
> Url: https://github.com/boostorg/mpi/pull/45#issuecomment-282054286  

That's the number of target you get when mpi is not activated in boost.  
In project-config.jam, you should have a   
`using mpi ; # maybe with more options  
`  
line. Is that the case ?

---
