# #163 Fix warnings about freeing non-heap-allocated object [Merged]

> Username: Lastique  
> Created at: 2022-06-09 20:25:59 UTC  
> Updated at: 2022-06-16 23:06:49 UTC  
> Merged at: 2022-06-16 23:02:59 UTC  
> Closed at: 2022-06-16 23:02:59 UTC  
> Url: https://github.com/boostorg/wave/pull/163  

This PR makes 3 commits:  
  
- Convert all checks for whether `pData_` points to `emptyString_` to checks for zero `capacity()`. This is a safer check, which should work across shared library boundaries.  
- Added compile-time asserts that `pData_` never points to `emptyString_` when `capacity()` is zero. This fixes https://github.com/boostorg/wave/issues/159.  
- Removes `AllocatorStringStorage::Realloc`. This function was not used and potentially incorrect, as it would call `Free` on the input buffer, even if it was `emptyString_`.  
  
I did not use `#pragma` to silence the warnings because it seems gcc 11.2 doesn't support disabling this warning (the warnings are still emitted).

---

## Review 1 [Approved]

> Username: jefftrull  
> Created_at: 2022-06-16 04:25:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/163#pullrequestreview-1008473142  

LGTM and will merge in 24h if @hkaiser has no objections. Would be good to get some CI feedback.

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2022-06-16 23:00:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/163#pullrequestreview-1009828985  

LGTM, thanks!

---
