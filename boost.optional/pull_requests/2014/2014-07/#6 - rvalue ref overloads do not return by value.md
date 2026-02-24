# #6 rvalue ref overloads do not return by value [Merged]

> Username: akrzemi1  
> Created at: 2014-07-14 08:45:57 UTC  
> Updated at: 2014-07-17 08:15:50 UTC  
> Merged at: 2014-07-17 08:15:50 UTC  
> Closed at: 2014-07-17 08:15:50 UTC  
> Url: https://github.com/boostorg/optional/pull/6  



---

## Comment 1

> Username: akrzemi1  
> Created_at: 2014-07-15 08:41:30 UTC  
> Url: https://github.com/boostorg/optional/pull/6#issuecomment-49004987  

Without this fix some overloads of operator\* will be returning by value and thereby incur an unnecessary move constructor or a copy constructor. It was trying to follow LEWG guidelines, but now LEWG realized the bug and changed the guidelines.

---
