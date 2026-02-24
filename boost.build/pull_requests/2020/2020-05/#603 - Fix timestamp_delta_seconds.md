# #603 Fix timestamp_delta_seconds [Merged]

> Username: Kojoley  
> Created at: 2020-05-11 17:03:22 UTC  
> Updated at: 2021-10-02 21:02:00 UTC  
> Merged at: 2020-05-12 12:31:34 UTC  
> Closed at: 2020-05-12 12:31:34 UTC  
> Url: https://github.com/boostorg/build/pull/603  

* The multiplier 1000000 is wrong  
* Return seconds as function name and description says  
* Use `difftime` instead of assuming `time_t` is in seconds  
  
Fixes #584

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-12 01:25:59 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/603#pullrequestreview-409638208  

Minor change.. Can you add a copyright line for yourself.

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-05-12 13:04:03 UTC  
> Url: https://github.com/boostorg/build/pull/603#issuecomment-627329573  

@Kojoley Thanks a lot!

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:59 UTC  
> Url: https://github.com/boostorg/build/pull/603#issuecomment-932820007  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
