# #1165 GPU Batch 4 [Merged]

> Username: mborland  
> Created at: 2024-07-29 19:44:00 UTC  
> Updated at: 2024-07-30 20:48:25 UTC  
> Merged at: 2024-07-30 20:48:22 UTC  
> Closed at: 2024-07-30 20:48:22 UTC  
> Url: https://github.com/boostorg/math/pull/1165  

Adds support for: exponential dist, landau dist, mapairy dist, holtsmark dist, saspoint5 dist, extreme value dist, laplace dist, and logistic dist.  
  
Adds new `boost::math::tools::pair` which is just an alias of `std::pair` or `thrust::pair` depending on context. Same goes for tuple and all the additional helper functions (e.g. make_pair). This fixes `range()` and `support()` functions for distributions on CUDA devices.   
  
Edit: @tk-yoshimura you may be interested in this since it adds both CUDA and SYCL support to your newly contributed distributions. The changes were fairly small with the biggest being recursion was eliminated from the `quantile` function to support SYCL.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-07-30 20:48:03 UTC  
> Url: https://github.com/boostorg/math/pull/1165#issuecomment-2259181890  

Only CI failure is a clone fail. The disk memory errors have been cleaned up as expected

---
