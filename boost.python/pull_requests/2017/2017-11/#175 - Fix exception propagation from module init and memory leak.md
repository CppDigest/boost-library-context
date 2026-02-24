# #175 Fix exception propagation from module init and memory leak [Open]

> Username: elmopl  
> Created at: 2017-11-07 09:29:47 UTC  
> Updated at: 2020-11-14 02:30:48 UTC  
> Url: https://github.com/boostorg/python/pull/175  

Two fixes:  
  
1. For Python3 when exception got raised in module init it got suppressed (see details in 7c3240d)  
2. Minor memory leak in `type_id.cpp:gcc_demangle` (see details in 1d19e6f)

---
