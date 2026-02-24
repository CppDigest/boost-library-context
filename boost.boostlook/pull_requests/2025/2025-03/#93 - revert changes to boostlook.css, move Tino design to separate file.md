# #93 revert changes to boostlook.css, move Tino design to separate file [Merged]

> Username: julioest  
> Created at: 2025-03-13 19:57:14 UTC  
> Updated at: 2025-03-13 21:15:44 UTC  
> Merged at: 2025-03-13 21:15:44 UTC  
> Closed at: 2025-03-13 21:15:44 UTC  
> Url: https://github.com/boostorg/boostlook/pull/93  

Preserves both design systems while fixing file organization:  
- moved tino design to new boostlook_tino.css file  
- reverted boostlook.css to original pre-tino version  
- removed boostlook_old.css backup

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-03-13 20:03:40 UTC  
> Url: https://github.com/boostorg/boostlook/pull/93#issuecomment-2722569140  

An automated preview of the documentation is available at [https://93.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://93.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 2 [Approved]

> Username: sdarwin  
> Created_at: 2025-03-13 20:17:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/93#pullrequestreview-2683273901  

"reverted boostlook.css to original pre-tino version".     
The target is `develop`.    
It sounds like that will revert boostlook on staging to a pre-tino version.       
The `master` branch doesn't yet have the changes.    
If you believe these are the right steps, yes ok.

---

## Review 3 [Approved]

> Username: rbbeeston  
> Created_at: 2025-03-13 21:05:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/93#pullrequestreview-2683365186  

LGTM

---
