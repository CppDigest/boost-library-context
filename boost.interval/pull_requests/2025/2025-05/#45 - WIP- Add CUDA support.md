# #45 WIP: Add CUDA support [Open]

> Username: lorenzgillner  
> Created at: 2025-05-13 09:40:33 UTC  
> Updated at: 2025-09-04 22:12:32 UTC  
> Url: https://github.com/boostorg/interval/pull/45  

The goal of this patch is to provide support for CUDA-compatible GPUs. This is achieved by introducing a new rounding type that does not switch modes because CUDA provides arithmetic operations as explicitly rounded functions.  
  
This is still a work in progress.

---
