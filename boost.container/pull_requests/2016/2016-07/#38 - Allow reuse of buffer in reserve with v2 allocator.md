# #38 Allow reuse of buffer in reserve with v2 allocator [Merged]

> Username: palebedev  
> Created at: 2016-07-16 16:08:56 UTC  
> Updated at: 2020-12-19 22:46:32 UTC  
> Merged at: 2016-07-31 21:13:50 UTC  
> Closed at: 2016-07-31 21:13:50 UTC  
> Url: https://github.com/boostorg/container/pull/38  

allocation_command uses expand_{fwd,bwd} flags and its result is later compared with the old pointer, but the old pointer was not passed for reuse.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-07-31 21:14:00 UTC  
> Url: https://github.com/boostorg/container/pull/38#issuecomment-236457031  

Thanks for the patch.

---
