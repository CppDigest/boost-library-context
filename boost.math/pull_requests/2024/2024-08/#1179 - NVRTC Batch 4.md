# #1179 NVRTC Batch 4 [Merged]

> Username: mborland  
> Created at: 2024-08-14 14:36:24 UTC  
> Updated at: 2024-08-15 15:05:14 UTC  
> Merged at: 2024-08-15 15:05:10 UTC  
> Closed at: 2024-08-15 15:05:10 UTC  
> Url: https://github.com/boostorg/math/pull/1179  

Adds support for: `sin_pi`, `cos_pi`, `erf`, `erfc`, `erf_inv`, `erfc_inv`, `digamma`, and numerical constants.  
  
In order to get the policies working properly (tested via digamma) I added a section to error handling specific to NVRTC platforms. Effectively all policies become `ignore_error` because throwing and run time assertions are disallowed. I also applied the changes form #1022 to significantly simplify the promotion system, and make it properly usable for CUDA systems. I believe after this PR the door is open for most of the functions that are not already in libcu++ with `digamma` being the first proof of concept.  
  
The changes to enable the numerical constants assumes that nobody is using anything bigger than double on device. I doubt we need big numbers on device, but we can address it in the future if someone opens an issue.  
  
Completed CI runs can be found here: https://github.com/cppalliance/cuda-math/pull/12  
  
CC: @jzmaddock since this one is contains actual significant changes unlike most of the CUDA PRs.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-08-15 15:05:04 UTC  
> Url: https://github.com/boostorg/math/pull/1179#issuecomment-2291482867  

CI Failures are a few failures to clone. As always should be safe to merge.

---
