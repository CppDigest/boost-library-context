# #712 fix: Broken build of test project for gcc-5 [Merged]

> Username: marco-langer  
> Created at: 2022-07-23 15:07:35 UTC  
> Updated at: 2022-07-23 18:21:28 UTC  
> Merged at: 2022-07-23 18:21:28 UTC  
> Closed at: 2022-07-23 18:21:28 UTC  
> Url: https://github.com/boostorg/gil/pull/712  

### Description  
  
This PR fixes the broken build of the test project for gcc-5 and closes thereby #709 and #710.  
  
Maybe this PR will also close #711. I had difficulties to reproduce the bug, as using godbolt [does not reproduce this bug using clang 3.9](https://godbolt.org/z/PxcTEEWq1).  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-07-23 18:19:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/712#pullrequestreview-1048559157  

Awesome! Thank you @marco-langer

---
