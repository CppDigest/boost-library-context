# #1396 Retire ubuntu 20.04 [Merged]

> Username: vissarion  
> Created at: 2025-04-24 11:42:58 UTC  
> Updated at: 2025-07-16 15:53:35 UTC  
> Merged at: 2025-04-24 20:28:07 UTC  
> Closed at: 2025-04-24 20:28:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1396  

Retire ubuntu 20.04 from github actions. This force us to also remove clang versions > 6 and gcc < 7, since it is not straightforward to install them in ubuntu 22.04. In particular, if one tries to import the ubuntu 18 (bionic) archives then `apt install` reaches an impossible situation when trying to install older versions (e.g. clang 5.0) due to unmet dependencies.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2025-04-24 16:25:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1396#pullrequestreview-2791822785  

super, thanks

---
