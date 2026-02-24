# #1431 Use the dynamic type while calling epsilon(). [Closed]

> Username: totakura  
> Created at: 2025-09-15 10:12:17 UTC  
> Updated at: 2025-09-16 16:05:53 UTC  
> Closed at: 2025-09-15 11:50:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1431  

Current call to epsilon is made with the assumption that the dynamic type `content_type` is compatible with double. This does not hold true for numeric types that are bigger than double, e.g: `absl::uint128`. This CL fixes it by binding the call to `content_type`'s type.  
  
This fixes #1430.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2025-09-16 14:17:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1431#issuecomment-3298982494  

@totakura why is this PR closed?

---

## Comment 2

> Username: totakura  
> Created_at: 2025-09-16 16:05:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1431#issuecomment-3299437954  

@barendgehrels I made a mistake in configuring my git repo and made a commit using my private email. Since I contributed to this work as part of my employment, I have to use my employer email address instead. So I closed this PR and created the other one (#1432) with my employer email address.

---
