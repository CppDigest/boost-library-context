# #901 Fix tanh_sinh integration for edge case, [Merged]

> Username: jzmaddock  
> Created at: 2022-12-11 13:11:30 UTC  
> Updated at: 2022-12-11 23:18:36 UTC  
> Merged at: 2022-12-11 16:27:41 UTC  
> Closed at: 2022-12-11 16:27:41 UTC  
> Url: https://github.com/boostorg/math/pull/901  

where max_left_index/max_right_index may go to zero if the function being integrated overflows the FP type being used across nearly all of it's range.  Fixes https://github.com/boostorg/math/issues/898

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2022-12-11 14:55:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/901#pullrequestreview-1212709469  

Looks good to me. I'll close the other PR.

---
