# #494 Allow bootstrapping with "borland" (Embarcadero) [Merged]

> Username: tanzislam  
> Created at: 2021-03-20 13:20:16 UTC  
> Updated at: 2021-03-20 18:09:40 UTC  
> Merged at: 2021-03-20 18:09:34 UTC  
> Closed at: 2021-03-20 18:09:34 UTC  
> Url: https://github.com/boostorg/boost/pull/494  

Pass down user's toolset override to the `b2`-building script. This was (most likely erroneously) removed in 26232c8 along with the output redirection.  
  
If user provided toolset of "borland" for bootstrapping, prepare `project-config.jam` for the modern `embarcadero` toolset.

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-03-20 18:09:40 UTC  
> Url: https://github.com/boostorg/boost/pull/494#issuecomment-803436934  

Thanks!

---
