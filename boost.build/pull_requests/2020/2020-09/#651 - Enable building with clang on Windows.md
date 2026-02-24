# #651 Enable building with clang on Windows [Merged]

> Username: Gei0r  
> Created at: 2020-09-04 12:25:29 UTC  
> Updated at: 2021-10-02 21:00:15 UTC  
> Merged at: 2020-09-08 01:01:19 UTC  
> Closed at: 2020-09-08 01:01:19 UTC  
> Url: https://github.com/boostorg/build/pull/651  

This adds awareness for building `b2` with `clang++` on Windows.  
The flags are the same as for `g++`.  
  
Note that this depends on https://github.com/boostorg/boost/commit/202c67acdd6033c431a2b6ad776bdf480be898d8, which is not yet in boost's master branch (see https://github.com/boostorg/boost/issues/427).

---

## Comment 1

> Username: Gei0r  
> Created_at: 2020-12-21 14:42:11 UTC  
> Url: https://github.com/boostorg/build/pull/651#issuecomment-749007845  

Why has this change not landed in boost 1.75.0?

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:14 UTC  
> Url: https://github.com/boostorg/build/pull/651#issuecomment-932819762  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
