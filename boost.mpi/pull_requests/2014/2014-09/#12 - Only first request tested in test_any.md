# #12 Only first request tested in test_any [Merged]

> Username: aminiussi  
> Created at: 2014-09-10 09:06:41 UTC  
> Updated at: 2014-09-16 11:42:01 UTC  
> Merged at: 2014-09-16 11:41:56 UTC  
> Closed at: 2014-09-16 11:41:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/12  

In the for loop, we increment first, but actually test current... Probably went unnoticed since the first one usually ends up completing.

---
