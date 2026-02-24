# #530 Remove unnecessary numeric_cast in hsv.hpp [Merged]

> Username: Mike-Devel  
> Created at: 2020-11-19 15:22:27 UTC  
> Updated at: 2020-11-22 21:51:43 UTC  
> Merged at: 2020-11-22 20:42:30 UTC  
> Closed at: 2020-11-22 20:42:30 UTC  
> Url: https://github.com/boostorg/gil/pull/530  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
Removes an unnecessary numeric_cast in include\boost\gil\extension\toolbox\color_spaces\hsv.hpp that casts from float32_t to float32_t.  
This removes the only dependency on Boost.NumericConversion I could find  
  
### References  
  
https://cpplang.slack.com/archives/C27KZLB0X/p1605793604490300  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-11-22 20:42:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/530#pullrequestreview-536112941  

LGTM. Thanks!

---
