# #599 Remove leftover manifest generation early exiting [Merged]

> Username: Kojoley  
> Created at: 2020-05-08 16:25:06 UTC  
> Updated at: 2021-10-02 21:01:57 UTC  
> Merged at: 2020-05-12 12:31:03 UTC  
> Closed at: 2020-05-12 12:31:03 UTC  
> Url: https://github.com/boostorg/build/pull/599  

It should have been removed in dd3dbcc9b6d4d6322d693f58bb4306930fb19f68

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-05-08 17:03:42 UTC  
> Url: https://github.com/boostorg/build/pull/599#issuecomment-625916931  

I also pushed removed unneeded manifest existence check. The commit it was added in d8e98e3abc18dc328a759f6f4bc71741687bc272 says it is fixing the problem that MSVC 8.0 does not produce a manifest file for static libraries, but the code it touches is for shared libraries and executables. And collapsed the branches as they became the same.

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-12 01:28:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/599#pullrequestreview-409638895  

Minor change.. Can you add a copyright line for yourself.

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:56 UTC  
> Url: https://github.com/boostorg/build/pull/599#issuecomment-932819997  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
