# #872 [traverse] modernize code and add documentation, make clusters const [Merged]

> Username: barendgehrels  
> Created at: 2021-06-23 12:20:32 UTC  
> Updated at: 2021-07-04 00:24:14 UTC  
> Merged at: 2021-06-30 08:24:30 UTC  
> Closed at: 2021-06-30 08:24:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/872  

This documents traversal switch detector for purpose of #869 but it doesn't solve it yet.  
It's clean-up only.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-06-24 09:19:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/872#pullrequestreview-691528180  

This is fine with me. Thanks.

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-06-24 16:05:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/872#pullrequestreview-691954751  

📁 include/boost/geometry/algorithms/detail/overlay/traversal_switch_detector.hpp

```diff
 100 |-         isolation_type isolated;
 123 |+         signed_size_type region_id{-1};
 124 |+         isolation_type isolated{isolation_unknown};
```

> Username: awulkiew  
> Created_at: 2021-06-24 16:05:37 UTC  
> Updated_at: 2021-06-24 16:05:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/872#discussion_r658085869  

I'm just curious. These are native type and enum, correct? Why have you decided to use braces instead of `=`?  
```  
signed_size_type region_id = -1;  
isolation_type isolated = isolation_unknown;  
```

> Username: barendgehrels  
> Created_at: 2021-06-24 16:51:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/872#discussion_r658121392  

Ah, it's my new habit now. Because we do it at the office. But I now remember that you and @mloskot objected to it, and indeed it does not make sense. I'll change it.

> Username: barendgehrels  
> Created_at: 2021-06-30 08:24:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/872#discussion_r661241196  

Updated :heavy_check_mark:

> Username: awulkiew  
> Created_at: 2021-06-30 09:28:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/872#discussion_r661289603  

Right, I now remember that we talked about this. So this is a habit. I was curious that maybe there is rationale behind it that I'm not aware of. Anyway, thanks!


---
