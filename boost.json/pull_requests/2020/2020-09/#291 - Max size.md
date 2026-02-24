# #291 Max size [Closed]

> Username: vinniefalco  
> Created at: 2020-09-06 20:22:22 UTC  
> Updated at: 2020-09-06 22:10:31 UTC  
> Closed at: 2020-09-06 22:09:03 UTC  
> Url: https://github.com/boostorg/json/pull/291  

fixes the problem with limits

---

## Review 1 [Commented]

> Username: sdkrystian  
> Created_at: 2020-09-06 21:30:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/291#pullrequestreview-483174028  

Looks good. Though, we should just define `max_size` as `soft_limit`.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-09-06 21:31:48 UTC  
> Url: https://github.com/boostorg/json/pull/291#issuecomment-687907181  

> max_size as soft_limit.  
  
That is something we can explore in the future, but note that there is a circular dependency loop of include files, so writing `sizeof(key_value_pair)` in object.hpp will be challenging.

---
