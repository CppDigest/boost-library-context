# #108 Feature/101 ibcast [Open]

> Username: aminiussi  
> Created at: 2019-10-28 21:11:43 UTC  
> Updated at: 2019-11-15 19:27:30 UTC  
> Url: https://github.com/boostorg/mpi/pull/108  



---

## Review 1 [Commented]

> Username: FreddieWitherden  
> Created_at: 2019-11-15 19:27:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mpi/pull/108#pullrequestreview-317822711  

📁 test/Jamfile.v2

```diff
  52 |+   # Intel MPI 2018 and older are expected to fail:
  53 |   [ mpi-test non_blocking_any_source : : : 2 17 ]
  54 |+   # Non Blockin common (MPI 3 only)
```

> Username: FreddieWitherden  
> Created_at: 2019-11-15 19:27:30 UTC  
> Url: https://github.com/boostorg/mpi/pull/108#discussion_r346976670  

s/Blockin/Blocking/


---
