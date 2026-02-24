# #1202 Various minor improvements [Merged]

> Username: barendgehrels  
> Created at: 2023-09-14 17:20:25 UTC  
> Updated at: 2024-12-14 11:14:15 UTC  
> Merged at: 2023-09-29 11:00:34 UTC  
> Closed at: 2023-09-29 11:00:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1202  

Fixes issue #1169   
Adds unit tests for issue #1138 which was already fixed  
Adds unit test for issue #1103 which was already fixed  
Adds comments for epsilon as suggested by @vissarion   
And some stylistic issues (const placements, includes)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2023-09-14 17:23:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1202#pullrequestreview-1627418118  

📁 include/boost/geometry/io/dsv/write.hpp

```diff
  28 | #include <boost/range/end.hpp>
  29 | #include <boost/range/value_type.hpp>
  30 |+ #include <boost/range/size.hpp>
```

> Username: barendgehrels  
> Created_at: 2023-09-14 17:23:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1202#discussion_r1326300792  

After updating Boost to the most recent develop on my notebook (because of swap), this missing include was problematic for some unit tests, which is the reason why I checked all usages.

> Username: vissarion  
> Created_at: 2023-09-25 12:47:07 UTC  
> Updated_at: 2023-09-25 12:50:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1202#discussion_r1335840659  

indeed, this is caught by github actions too, which is still failing after the modifications of this PR.


---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2023-09-25 13:05:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1202#pullrequestreview-1642121279  

Thanks! I am OK in general but I think it's worth it to fix the missing header issue.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2023-09-29 09:49:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1202#issuecomment-1740616992  

> Thanks! I am OK in general but I think it's worth it to fix the missing header issue.  
  
For me it did not fail. But I added the include there too and it should now be fine. Thank you.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2023-09-29 10:56:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1202#issuecomment-1740702076  

Now all tests pass. @vissarion could you approve?

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2023-09-29 10:57:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1202#pullrequestreview-1650554728  

Sure, thanks!

---
