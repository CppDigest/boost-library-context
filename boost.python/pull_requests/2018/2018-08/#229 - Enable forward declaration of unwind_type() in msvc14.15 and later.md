# #229 Enable forward declaration of unwind_type() in msvc14.15 and later. [Merged]

> Username: bowie7070  
> Created at: 2018-08-16 20:09:21 UTC  
> Updated at: 2019-03-25 15:00:55 UTC  
> Merged at: 2019-03-25 15:00:49 UTC  
> Closed at: 2019-03-25 15:00:49 UTC  
> Url: https://github.com/boostorg/python/pull/229  

Name lookup in msvc has changed between 14.14 and 14.15 making it consistent with other compilers. Forward declaration of unwind_type() is now required as it is for other compilers.  
  
Resolves compilation errors identified in:  
  
https://github.com/boostorg/python/issues/228

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2019-03-25 15:00:55 UTC  
> Url: https://github.com/boostorg/python/pull/229#issuecomment-476235832  

Thanks !

---
