# #1432 Use the dynamic type while calling epsilon(). [Merged]

> Username: totakura  
> Created at: 2025-09-15 11:49:34 UTC  
> Updated at: 2025-10-09 17:57:27 UTC  
> Merged at: 2025-10-09 17:57:27 UTC  
> Closed at: 2025-10-09 17:57:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1432  

Current call to epsilon is made with the assumption that the dynamic type `content_type` is compatible with double. This does not hold true for numeric types that are bigger than double, e.g: `absl::uint128`. This CL fixes it by binding the call to `content_type`'s type.  
  
This fixes https://github.com/boostorg/geometry/issues/1430.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2025-09-16 14:21:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1432#pullrequestreview-3230259653  

Thanks, looks OK to me  
(and now I see there were 2 PR's so that's why the other is closed)

---

## Comment 2

> Username: totakura  
> Created_at: 2025-09-16 16:07:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1432#issuecomment-3299445636  

Made a mistake with the author email used to commit in the other PR. The current PR uses the correct email with my employer domain.

---

## Review 3 [Approved]

> Username: tinko92  
> Created_at: 2025-09-24 10:40:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1432#pullrequestreview-3262261161  

This looks good to me. epsilon for some custom integer type will be zero, so same behaviour as comparing to ~1e-16. It requires any content_type to have a numeric_limits specialisation but the header already requires that anyway.  
  
This comparison to an unscaled epsilon looks dubious in general, but that's unrelated to the PR.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-10-09 17:57:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1432#issuecomment-3386953589  

I'll merge it.

---
