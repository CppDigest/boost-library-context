# #1334 Fix circular dependency on Master [Merged]

> Username: mborland  
> Created at: 2025-10-30 11:54:44 UTC  
> Updated at: 2025-10-30 15:18:39 UTC  
> Merged at: 2025-10-30 15:18:39 UTC  
> Closed at: 2025-10-30 15:18:39 UTC  
> Url: https://github.com/boostorg/math/pull/1334  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-10-30 13:36:38 UTC  
> Url: https://github.com/boostorg/math/pull/1334#issuecomment-3468047510  

Hi Matt. Do you know if anything is needed in Multiprecision for this adaption?

---

## Comment 2

> Username: mborland  
> Created_at: 2025-10-30 15:18:03 UTC  
> Url: https://github.com/boostorg/math/pull/1334#issuecomment-3468556154  

> Hi Matt. Do you know if anything is needed in Multiprecision for this adaption?  
  
Fortunately nothing is needed. MP can include Math just fine so long as Math does not include MP (in the include/ directory that is.)

---
