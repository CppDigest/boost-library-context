# #336 fix CUDA header for runtime api [Open]

> Username: m-fila  
> Created at: 2026-01-29 13:21:36 UTC  
> Updated at: 2026-01-29 13:21:36 UTC  
> Url: https://github.com/boostorg/fiber/pull/336  

The CUDA types (`cudaStream_t`, `cudaError_t`)  and functions `cudaStreamAddCallback` used are part of CUDA runtime API but the included header `cuda.h` is for CUDA driver API.  
  
This gives a compilation error in client code compiled with host compiler unless `cuda_runtime_api.h` (CUDA runtime API  header) is included before `boost/fiber/`  
The examples in this repo compile fine, but they are compiled with `nvcc` which takes care of CUDA includes anyway

---
