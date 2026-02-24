# #53 Drone: split GCC 13 jobs [Closed]

> Username: sdarwin  
> Created at: 2024-04-17 21:59:38 UTC  
> Updated at: 2024-04-18 15:35:47 UTC  
> Closed at: 2024-04-18 08:50:22 UTC  
> Url: https://github.com/boostorg/endian/pull/53  

@pdimov after provisioning 4GB of swap space on all agents, the "Linux 23.04 GCC 13 64 ASAN" job still eventually crashed.  
  
New strategy: if a job is freezing, first split it into two parts 32-bit, 64-bit.   Next, if the problem continues, break it into pieces CXXSTD: '11' , CXXSTD: '14', CXXSTD: '17' and so on.   That has solved the problem in this case.  
  
Since 23.04 is experimental, not LTS, and this is an ASAN test, there are too many variables to make a judgment.  If the same thing happens on 24.04, without ASAN, it could be an argument that something needs to be fixed.    
  
When there are only a few tests that consume a large amount of memory, try this tactic first.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-18 03:34:56 UTC  
> Url: https://github.com/boostorg/endian/pull/53#issuecomment-2062929621  

Same thing happens on 22.04 (which is an LTS) with GCC 12 ASAN.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-18 08:00:16 UTC  
> Updated_at: 2024-04-18 08:50:32 UTC  
> Url: https://github.com/boostorg/endian/pull/53#issuecomment-2063261318  

The problem here is most likely the `./b2 -j3` part, which is solved by splitting only coincidentally (as there are no longer three jobs compiling that one problematic source file.)  
  
I'll change it to -j2 and if this doesn't help, to -j1.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-18 08:50:22 UTC  
> Url: https://github.com/boostorg/endian/pull/53#issuecomment-2063358105  

-j2 seems to have worked.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2024-04-18 15:35:45 UTC  
> Url: https://github.com/boostorg/endian/pull/53#issuecomment-2064267004  

Drone agents now get 8GB swap. 16GB total 'virtual memory'.    
Restarted the last failed job, and it completed.

---
