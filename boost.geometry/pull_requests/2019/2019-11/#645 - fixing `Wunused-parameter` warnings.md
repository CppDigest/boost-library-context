# #645 fixing `Wunused-parameter` warnings [Merged]

> Username: vissarion  
> Created at: 2019-11-19 10:29:06 UTC  
> Updated at: 2021-06-30 14:04:52 UTC  
> Merged at: 2019-11-19 13:48:55 UTC  
> Closed at: 2019-11-19 13:48:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/645  



---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2019-11-19 11:04:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/645#pullrequestreview-318951563  

📁 include/boost/geometry/strategies/cartesian/buffer_end_round.hpp

```diff
 117 |                 RangeOut& range_out) const
 118 |     {
 119 |+         boost::ignore_unused(perp_left_point);
```

> Username: mloskot  
> Created_at: 2019-11-19 11:04:14 UTC  
> Updated_at: 2019-11-19 11:09:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/645#discussion_r347860815  

Shouldn't there be explicit `#include <boost/core/ignore_unused.hpp>` for this, to keep the header self-contained, etc.?

> Username: vissarion  
> Created_at: 2019-11-19 11:11:22 UTC  
> Updated_at: 2019-11-19 11:11:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/645#discussion_r347863794  

Yes, this is better. Changed. Thanks!

> Username: barendgehrels  
> Created_at: 2019-11-23 11:45:38 UTC  
> Updated_at: 2019-11-23 11:45:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/645#discussion_r349869398  

Thanks!


---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-11-19 13:48:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/645#issuecomment-555515335  

Thanks!

---
