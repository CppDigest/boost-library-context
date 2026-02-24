# #442 Allow bootstrapping with "borland" (Embarcadero) [Closed]

> Username: tanzislam  
> Created at: 2020-12-03 13:10:04 UTC  
> Updated at: 2021-03-20 13:20:58 UTC  
> Closed at: 2021-03-20 00:28:31 UTC  
> Url: https://github.com/boostorg/boost/pull/442  

Pass down user's toolset override to the `b2`-building script. This was (most likely erroneously) removed in 26232c8d4 along with the output redirection.  
  
If user provided toolset of "borland" for bootstrapping, prepare `project-config.jam` for the modern `embarcadero` toolset.

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-03-20 00:28:11 UTC  
> Url: https://github.com/boostorg/boost/pull/442#issuecomment-803206358  

Can you open this pull request against the develop branch instead, please?

---

## Comment 2

> Username: tanzislam  
> Created_at: 2021-03-20 13:20:58 UTC  
> Url: https://github.com/boostorg/boost/pull/442#issuecomment-803318019  

Replaced with #494.

---
