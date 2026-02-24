# #348 Remove ::boost::test_tools::tt_detail::dummy_cond [Merged]

> Username: jspam  
> Created at: 2022-07-08 10:44:08 UTC  
> Updated at: 2024-10-04 17:48:17 UTC  
> Merged at: 2024-10-04 17:48:17 UTC  
> Closed at: 2024-10-04 17:48:17 UTC  
> Url: https://github.com/boostorg/test/pull/348  

Replace loops using it by simple do { ... } while (0) loops.  
This allows static analysis tools like clang-tidy to better understand  
the code and avoid false positives.  
  
Fixes #343.

---

## Review 1 [Approved]

> Username: jonesmz  
> Created_at: 2024-10-04 15:50:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/348#pullrequestreview-2348395940  

I spent the better part of several days fighting clang-tidy false positives caused by this.  
  
Can this change please be merged for the next boost release?

---

## Comment 2

> Username: mborland  
> Created_at: 2024-10-04 16:03:32 UTC  
> Url: https://github.com/boostorg/test/pull/348#issuecomment-2394024136  

> I spent the better part of several days fighting clang-tidy false positives caused by this.  
>   
> Can this change please be merged for the next boost release?  
  
I merge in develop to kick off a CI run to validate. The changes look like they should be OK.

---
