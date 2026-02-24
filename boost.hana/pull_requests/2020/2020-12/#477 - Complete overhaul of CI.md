# #477 Complete overhaul of CI [Merged]

> Username: ldionne  
> Created at: 2020-12-30 20:09:58 UTC  
> Updated at: 2021-05-26 14:59:03 UTC  
> Merged at: 2021-05-26 14:58:47 UTC  
> Closed at: 2021-05-26 14:58:47 UTC  
> Url: https://github.com/boostorg/hana/pull/477  

This should allow us to use more recent compilers, and test C++2a support.

---

## Review 1 [Commented]

> Username: keryell  
> Created_at: 2020-12-30 22:31:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/477#pullrequestreview-560219466  

📁 .travis.yml

```diff
   6 | os: linux # Jobs are on Linux unless specified otherwise
   7 |- dist: trusty # Jobs are on Trusty unless specified otherwise
   7 |+ dist: xenial # Jobs are on Xenial unless specified otherwise
```

> Username: keryell  
> Created_at: 2020-12-30 22:31:28 UTC  
> Updated_at: 2020-12-30 22:31:29 UTC  
> Url: https://github.com/boostorg/hana/pull/477#discussion_r550345257  

Why only `xenial`? You could use for example `focal` to get even more modern compiler and so on.


---
