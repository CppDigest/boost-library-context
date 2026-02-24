# #487 Fix interleaved_view factory using point<std::ptrdiff_t> for dimension [Merged]

> Username: sdebionne  
> Created at: 2020-04-22 17:05:41 UTC  
> Updated at: 2020-04-27 07:41:18 UTC  
> Merged at: 2020-04-23 20:41:31 UTC  
> Closed at: 2020-04-23 20:41:31 UTC  
> Url: https://github.com/boostorg/gil/pull/487  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Stick to the existing convention of `ptrdiff_t` and ensure it is used consistently across the existing code and new code.  
  
### References  
  
https://cpplang.slack.com/archives/CSVT0STV2/p1587562275123700  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-04-22 19:47:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/487#pullrequestreview-398528145  

LGTM, thanks!

---
