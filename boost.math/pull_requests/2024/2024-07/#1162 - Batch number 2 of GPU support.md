# #1162 Batch number 2 of GPU support [Merged]

> Username: mborland  
> Created at: 2024-07-24 20:51:50 UTC  
> Updated at: 2024-07-26 15:05:41 UTC  
> Merged at: 2024-07-26 15:05:38 UTC  
> Closed at: 2024-07-26 15:05:38 UTC  
> Url: https://github.com/boostorg/math/pull/1162  

Adds CUDA and SYCL support to the following: Cauchy distribution, cbrt, sign manipulation functions, trunc, round, modf.  
  
Changes the default policy of CUDA to match that of SYCL and adds GPU specific handling to the overflow ignore error handler (i.e. gets rid of use of `std::numeric_limits`)

---

## Comment 1

> Username: mborland  
> Created_at: 2024-07-25 12:15:56 UTC  
> Url: https://github.com/boostorg/math/pull/1162#issuecomment-2250183093  

The drone build on this is actually green: https://drone.cpp.al/boostorg/math/1975. Not sure why it's still listed as a pending workflow. @jzmaddock let me know if you have any comments. This one has fewer substantial changes than the previous batch.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-07-26 15:05:07 UTC  
> Url: https://github.com/boostorg/math/pull/1162#issuecomment-2252959293  

I think this is safe to merge. In the next batch I wrote my own complete version of `limits` to get around the fact that libcu++ does not provide it which significantly simplifies some of the changes. Once it's here in math I'll ask libcu++ if they want me to port it to them.

---
