# #1180 NVRTC Batch 5 [Merged]

> Username: mborland  
> Created at: 2024-08-15 15:15:43 UTC  
> Updated at: 2024-08-19 14:26:38 UTC  
> Merged at: 2024-08-16 13:36:46 UTC  
> Closed at: 2024-08-16 13:36:46 UTC  
> Url: https://github.com/boostorg/math/pull/1180  

Finally, we have enough foundation on NVRTC that we can now add distributions.  
  
Adds NVRTC support to: arcsine dist, bernoulli dist, cauchy dist, exponential dist, extreme value dist, holtsmark dist, landau dist, laplace dist, logistic dist, mapairy dist, and saspoint5 dist. This is the same support that currently exists for NVCC  
  
Replaces the use of `thurst::pair` with `cuda::std::pair` so that it works with NVRTC. Implement our own versions of `cuda::std::forward` and `cuda::std::make_tuple` since they are missing from libcu++.  
  
NVRTC support is almost in line with what is supported in NVCC and SYCL minus a few outstanding special functions that are not in the libcu++ library. Those will be in the next batch so that development moving forward will apply to all platforms at once rather than piecemeal.  
  
Completed CI runs on device can be found at: https://github.com/cppalliance/cuda-math/pull/13  
  
CC: @steppi, @izaid, @dschmitz89

---

## Comment 1

> Username: izaid  
> Created_at: 2024-08-15 15:19:54 UTC  
> Url: https://github.com/boostorg/math/pull/1180#issuecomment-2291508654  

This is really, really great! Thanks!!

---

## Comment 2

> Username: dschmitz89  
> Created_at: 2024-08-15 20:11:23 UTC  
> Url: https://github.com/boostorg/math/pull/1180#issuecomment-2292136289  

Same here, on behalf of the Python community, thank you very much, this whole undertaking will be an important step for reusable special functions throughout the ecosystem!

---

## Comment 3

> Username: mborland  
> Created_at: 2024-08-16 13:36:40 UTC  
> Url: https://github.com/boostorg/math/pull/1180#issuecomment-2293527030  

Only CI failure is a bad connect downloading packages. Should be safe to merge.

---

## Comment 4

> Username: steppi  
> Created_at: 2024-08-19 14:19:12 UTC  
> Url: https://github.com/boostorg/math/pull/1180#issuecomment-2296696946  

Excellent! I'll start looking into getting this working in CuPy.

---

## Comment 5

> Username: mborland  
> Created_at: 2024-08-19 14:26:37 UTC  
> Url: https://github.com/boostorg/math/pull/1180#issuecomment-2296714292  

> Excellent! I'll start looking into getting this working in CuPy.  
  
Let us know how it goes and feel free to ping us with issues you find.

---
