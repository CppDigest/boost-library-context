# #57 Bug with move ctor signature [Merged]

> Username: aminiussi  
> Created at: 2018-04-09 13:37:22 UTC  
> Updated at: 2018-04-18 14:36:29 UTC  
> Merged at: 2018-04-18 14:36:12 UTC  
> Closed at: 2018-04-18 14:36:12 UTC  
> Url: https://github.com/boostorg/mpi/pull/57  

Modify the signature of the move ctor to avoid infinite loop.  
Test move ctor on cartesian topology.  
refs #57

---

## Comment 1

> Username: aminiussi  
> Created_at: 2018-04-18 14:30:30 UTC  
> Url: https://github.com/boostorg/mpi/pull/57#issuecomment-382407168  

This sound trivial enough, it's just conditional compilation en adding test.  
  
I'll merge it so we can have testing on a variety of platforms.

---
