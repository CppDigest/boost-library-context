# #491 Improve error message on standalone if missing Boost.Config [Merged]

> Username: mborland  
> Created at: 2022-08-31 15:49:39 UTC  
> Updated at: 2022-12-16 03:43:08 UTC  
> Merged at: 2022-09-01 17:19:01 UTC  
> Closed at: 2022-09-01 17:19:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/491  

Fixes #490

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-08-31 17:18:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/491#issuecomment-1233211075  

The CircleCI failure is fixed in https://github.com/boostorg/quickbook/commit/071a58488d265c8a3f4d539196ee16c8db7e2769, which is to say not our problem.  
  
Thanks for this Matt!

---

## Comment 2

> Username: mborland  
> Created_at: 2022-09-01 15:55:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/491#issuecomment-1234473800  

@jzmaddock This should be good now. Other than the CI failure you mentioned their is one stemming from a [concept failure in Boost.Filesystem.](https://github.com/boostorg/multiprecision/runs/8117686288?check_suite_focus=true#step:17:212)

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-09-01 17:18:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/491#issuecomment-1234566079  

Looks like https://github.com/boostorg/filesystem/commit/b219d9fb8af760b54b014d1223ebd5d43b4d07b5, I've left a comment.

---
