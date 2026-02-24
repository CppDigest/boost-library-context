# #1091 Fix mutable point issue and typos in boundary_checker. [Merged]

> Username: vissarion  
> Created at: 2022-11-11 16:00:40 UTC  
> Updated at: 2022-11-16 14:09:36 UTC  
> Merged at: 2022-11-16 14:09:32 UTC  
> Closed at: 2022-11-16 14:09:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1091  

Fixes https://github.com/boostorg/geometry/issues/1088

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2022-11-16 09:11:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1091#pullrequestreview-1182174023  

Thanks! Apparently we don't have a unit test for this yet (though we have `relate_const.cpp`...)

---

## Comment 2

> Username: vissarion  
> Created_at: 2022-11-16 13:49:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1091#issuecomment-1317052927  

> Thanks! Apparently we don't have a unit test for this yet (though we have `relate_const.cpp`...)  
  
I added another test with a custom point.

---
