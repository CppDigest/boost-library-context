# #880 Fix min-max extrema kernel code [Open]

> Username: e-kud  
> Created at: 2023-01-06 01:34:25 UTC  
> Updated at: 2024-12-28 14:26:16 UTC  
> Url: https://github.com/boostorg/compute/pull/880  

Due to ceiling of block size, the index may be out of bounds. As index is always used, we must have it valid regardless `while (index < end)` condition.

---

## Comment 1

> Username: e-kud  
> Created_at: 2024-12-28 14:26:15 UTC  
> Url: https://github.com/boostorg/compute/pull/880#issuecomment-2564337399  

@pdimov @kylelutz could you please take a look at this patch?

---
