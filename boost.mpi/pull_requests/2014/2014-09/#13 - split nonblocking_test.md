# #13  split nonblocking_test [Merged]

> Username: aminiussi  
> Created at: 2014-09-10 09:38:52 UTC  
> Updated at: 2014-09-16 10:02:18 UTC  
> Merged at: 2014-09-16 10:02:10 UTC  
> Closed at: 2014-09-16 10:02:10 UTC  
> Url: https://github.com/boostorg/mpi/pull/13  

There are two funtion in non blocking test, since there is non common code between mk_all and the other single tests.  
That make debgging (setting break points, single steping) longer for no benefit (as far as I could see).  
  
This fix simply split the function.

---
