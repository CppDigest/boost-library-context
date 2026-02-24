# #669 Fix memory leak in startup::bootstrap (boostorg#668) [Merged]

> Username: mst7555  
> Created at: 2020-11-06 15:53:44 UTC  
> Updated at: 2021-10-02 21:01:29 UTC  
> Merged at: 2020-11-15 03:22:20 UTC  
> Closed at: 2020-11-15 03:22:20 UTC  
> Url: https://github.com/boostorg/build/pull/669  

The executable_path allocates a string (strdup) which was never released  
  
Tested with ASAN and valgrind

---

## Comment 1

> Username: mst7555  
> Created_at: 2020-11-06 15:54:22 UTC  
> Url: https://github.com/boostorg/build/pull/669#issuecomment-723154554  

The problem appeared in 1.74 so it should be backported

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:28 UTC  
> Url: https://github.com/boostorg/build/pull/669#issuecomment-932819919  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
