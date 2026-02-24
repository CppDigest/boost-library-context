# #640 Add missing relevant features for searched lib targets (Fixes #515) [Merged]

> Username: sjcooke  
> Created at: 2020-07-27 19:22:14 UTC  
> Updated at: 2021-10-02 21:01:36 UTC  
> Merged at: 2020-09-02 02:52:45 UTC  
> Closed at: 2020-09-02 02:52:45 UTC  
> Url: https://github.com/boostorg/build/pull/640  

These are the changes discussed in #515, verified for both address-model=32,64 and release+debug examples.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2020-07-28 00:21:11 UTC  
> Url: https://github.com/boostorg/build/pull/640#issuecomment-664704667  

AMDG  
  
Can you add a regression test in test/searched-lib.py?  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: sjcooke  
> Created_at: 2020-07-29 16:30:38 UTC  
> Url: https://github.com/boostorg/build/pull/640#issuecomment-665768604  

I added a regression test that fails without the fix, tested using both msvc and gcc toolsets.

---

## Review 3 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-08-29 05:09:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/640#pullrequestreview-478099186  

Can you add a copyright statement for yourself please.

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:35 UTC  
> Url: https://github.com/boostorg/build/pull/640#issuecomment-932819934  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
