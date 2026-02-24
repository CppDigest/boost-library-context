# #710 [index] Use memcpy and memmove only if T is_trivially_copyable. [Merged]

> Username: awulkiew  
> Created at: 2020-05-01 22:12:14 UTC  
> Updated at: 2020-05-13 14:55:39 UTC  
> Merged at: 2020-05-13 14:55:39 UTC  
> Closed at: 2020-05-13 14:55:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/710  

In C++03 mimic the trait with has_trivial_copy, has_trivial_assign and has_trivial_destructor.  
  
This is a fix for https://github.com/boostorg/geometry/issues/709

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-05-13 13:50:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/710#pullrequestreview-410944562  

I am ok with this fix. Thanks!

---
