# #33 No comment after preprocessor continuation for msvc-12.0 [Merged]

> Username: jhunold  
> Created at: 2014-12-05 21:13:29 UTC  
> Updated at: 2014-12-11 06:22:10 UTC  
> Merged at: 2014-12-06 09:50:55 UTC  
> Closed at: 2014-12-06 09:50:55 UTC  
> Url: https://github.com/boostorg/thread/pull/33  

msvc does not like comment after macros continuation markers. This was broken in 14c5cff2eeb1454130b4313c8f22f1853c1f6acd and all tests are failing for all msvc versions since.

---

## Comment 1

> Username: viboes  
> Created_at: 2014-12-06 09:54:35 UTC  
> Url: https://github.com/boostorg/thread/pull/33#issuecomment-65891630  

Thanks for the patch.

---
