# #422 Fix UB in instance_dealloc [Closed]

> Username: vient  
> Created at: 2023-08-30 20:59:53 UTC  
> Updated at: 2024-09-22 16:31:10 UTC  
> Closed at: 2024-09-22 16:31:10 UTC  
> Url: https://github.com/boostorg/python/pull/422  

Do not use dynamic_cast after destroying object, it is not correct and leads to NULL pointer dereference with some compilers  
  
This fix worked for me, no more SIGSEGVs.  
  
Fix #421

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2024-09-22 16:31:10 UTC  
> Url: https://github.com/boostorg/python/pull/422#issuecomment-2366862678  

This was taken care of by #387

---
