# #443 Another fix for numpy 2.0 [Merged]

> Username: bkpoon  
> Created at: 2024-07-15 22:50:03 UTC  
> Updated at: 2024-07-16 18:15:45 UTC  
> Merged at: 2024-07-16 18:15:12 UTC  
> Closed at: 2024-07-16 18:15:12 UTC  
> Url: https://github.com/boostorg/python/pull/443  

Compare pointers directly instead of using PyArray_EquivTypes  
  
Fixes #376

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2024-07-16 18:15:43 UTC  
> Url: https://github.com/boostorg/python/pull/443#issuecomment-2231526305  

This looks indeed simple enough. Happy to see that this is all it takes. (Please disregard the CI build failures, they are due to a tool bug on OSX...  
Thanks !

---
