# #628 Fix distance pt-linestring for short segments [Merged]

> Username: vissarion  
> Created at: 2019-10-17 16:52:53 UTC  
> Updated at: 2021-06-30 14:07:57 UTC  
> Merged at: 2019-10-19 15:58:18 UTC  
> Closed at: 2019-10-19 15:58:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/628  

This PR proposes a fix for https://github.com/boostorg/geometry/issues/557

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2019-10-17 17:31:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/628#pullrequestreview-303423347  

📁 include/boost/geometry/algorithms/detail/closest_feature/point_to_range.hpp

```diff
  76 |+             // Stop only if we find exactly zero distance
  77 |+             // otherwise it may stop at some very small value and miss the min
  78 |+             if (dist == zero){
```

> Username: barendgehrels  
> Created_at: 2019-10-17 17:29:16 UTC  
> Updated_at: 2019-10-17 19:36:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/628#discussion_r336132977  

The curly brace should go to next line

> Username: barendgehrels  
> Created_at: 2019-10-17 17:31:03 UTC  
> Updated_at: 2019-10-17 19:36:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/628#discussion_r336133780  

The `dist` can probably be const (this was already but maybe you can change it on the go)  
`Distance const dist = ...`


---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-10-19 15:58:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/628#issuecomment-544164472  

Thanks!

---
