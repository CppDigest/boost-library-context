# #582 Build fixes and other [Merged]

> Username: jszuppe  
> Created at: 2016-04-10 11:08:02 UTC  
> Updated at: 2016-04-12 04:12:45 UTC  
> Merged at: 2016-04-12 04:12:34 UTC  
> Closed at: 2016-04-12 04:12:34 UTC  
> Url: https://github.com/boostorg/compute/pull/582  

- Now 0.13 version of POCL is used instead of `master`-branch version.  
- Fix for AMD APP SDK v3.0 builds, AMD updated the SDK and nothing works as it did :) I opened an [issue ](https://community.amd.com/thread/198900) on AMD forum, maybe they'll fix it later.  
- Minor fixes in `test_buffer`, `parameter_cache` and `perf_saxpy`

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-04-10 12:05:13 UTC  
> Url: https://github.com/boostorg/compute/pull/582#issuecomment-207971398  

[![Coverage Status](https://coveralls.io/builds/5729803/badge)](https://coveralls.io/builds/5729803)  
  
Coverage remained the same at 88.568% when pulling **c683d4ab2215281693eac80263112ec4101443da on haahh:misc_fixes** into **44f08e3932e8ec1101e1aa137e404c3da5fddb5c on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-04-12 04:12:45 UTC  
> Url: https://github.com/boostorg/compute/pull/582#issuecomment-208695017  

Awesome! Thanks for getting POCL working again!

---
