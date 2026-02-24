# #145 Fixes for x32 ABI. [Merged]

> Username: hvdijk  
> Created at: 2020-06-28 21:41:32 UTC  
> Updated at: 2020-06-29 06:30:15 UTC  
> Merged at: 2020-06-29 06:30:08 UTC  
> Closed at: 2020-06-29 06:30:08 UTC  
> Url: https://github.com/boostorg/context/pull/145  

In the x32 ABI, pointers take up 4 bytes, so a structure containing two pointers is passed and returned in a single 8-byte register.  
  
Changes required to build for the x32 ABI in the first place are not part of this pull request: that is the subject of https://github.com/boostorg/build/issues/388. I have tested this PR on top of local modifications that bypass Boost's forced overriding of compiler flags.

---

## Comment 1

> Username: olk  
> Created_at: 2020-06-29 06:30:14 UTC  
> Url: https://github.com/boostorg/context/pull/145#issuecomment-650951283  

ty

---
