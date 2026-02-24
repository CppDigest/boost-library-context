# #241 Speed up properties a bit [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2020-04-06 00:57:23 UTC  
> Updated at: 2021-03-01 06:48:34 UTC  
> Merged at: 2021-03-01 06:48:29 UTC  
> Closed at: 2021-03-01 06:48:29 UTC  
> Url: https://github.com/boostorg/fiber/pull/241  

- It's unlikely that properties are null, it is null only the very  
   first time the fiber is scheduled.  
 - We don't need the dynamic_cast in notify, we can already use the  
   right type, since it is used only with algorithm_with_properties  
   anyway.  
 - Allow user defined properties to specify they don't want to use  
   dynamic_cast but they prefer static_cast for performance reason  
   (and then it's their responsibility to ensure the types are ok).

---

## Comment 1

> Username: olk  
> Created_at: 2021-03-01 06:48:33 UTC  
> Url: https://github.com/boostorg/fiber/pull/241#issuecomment-787696924  

ty

---
