# #331 Restrict Intel intrinsics to Intel processors on msvc. [Merged]

> Username: jzmaddock  
> Created at: 2021-06-02 10:03:10 UTC  
> Updated at: 2021-12-14 14:38:54 UTC  
> Merged at: 2021-06-04 12:46:41 UTC  
> Closed at: 2021-06-04 12:46:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/331  

Fixes https://github.com/boostorg/multiprecision/issues/321.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-06-04 12:46:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/331#issuecomment-854697194  

Failures are spurious and fixed elsewhere... merging.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2021-12-14 14:38:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/331#issuecomment-993606666  

FWIU this did not fix the built on ARM64. Also, why on ARM64 the `intel_intrinsics.hpp` is included in the first place?

---
