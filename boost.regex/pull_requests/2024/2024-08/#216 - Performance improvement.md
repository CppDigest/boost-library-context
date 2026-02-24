# #216 Performance improvement [Merged]

> Username: BrianWeed  
> Created at: 2024-08-14 18:12:43 UTC  
> Updated at: 2024-08-24 10:34:43 UTC  
> Merged at: 2024-08-24 10:34:43 UTC  
> Closed at: 2024-08-24 10:34:43 UTC  
> Url: https://github.com/boostorg/regex/pull/216  

By-value sink params can be moved into their final destination

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-08-17 17:59:34 UTC  
> Url: https://github.com/boostorg/regex/pull/216#issuecomment-2294928361  

Given the nature of shared_ptr, I'm not sure this really gains much if at all?

---

## Comment 2

> Username: BrianWeed  
> Created_at: 2024-08-17 23:41:56 UTC  
> Url: https://github.com/boostorg/regex/pull/216#issuecomment-2295028501  

Making a copy of a shared_ptr increases (and then decreases at destruction) an atomic reference counter. These atomic operations are thread fences. Thread fences cause the optimizing compiler to reset, which causes performance issues.

---
