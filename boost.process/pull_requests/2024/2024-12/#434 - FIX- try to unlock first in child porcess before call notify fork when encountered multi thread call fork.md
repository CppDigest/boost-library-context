# #434 FIX: try to unlock first in child porcess before call notify fork when encountered multi thread call fork [Closed]

> Username: zhixingchen0629  
> Created at: 2024-12-03 03:34:12 UTC  
> Updated at: 2024-12-13 04:16:46 UTC  
> Closed at: 2024-12-13 04:16:46 UTC  
> Url: https://github.com/boostorg/process/pull/434  



---

## Comment 1

> Username: zhixingchen0629  
> Created_at: 2024-12-03 03:34:51 UTC  
> Url: https://github.com/boostorg/process/pull/434#issuecomment-2513476800  

https://github.com/boostorg/process/issues/430

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2024-12-13 04:16:46 UTC  
> Url: https://github.com/boostorg/process/pull/434#issuecomment-2540515763  

Calling a `detail` namespace of another lib and unlocking a mutex is not the way to solve this. I'll discuss in the issue.

---
