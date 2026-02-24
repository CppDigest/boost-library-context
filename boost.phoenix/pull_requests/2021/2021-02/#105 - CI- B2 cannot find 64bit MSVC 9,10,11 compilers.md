# #105 CI: B2 cannot find 64bit MSVC 9,10,11 compilers [Merged]

> Username: Kojoley  
> Created at: 2021-02-12 21:38:24 UTC  
> Updated at: 2021-03-08 13:59:12 UTC  
> Merged at: 2021-03-08 11:03:37 UTC  
> Closed at: 2021-03-08 11:03:37 UTC  
> Url: https://github.com/boostorg/phoenix/pull/105  

Refs https://github.com/boostorg/build/issues/659

---

## Comment 1

> Username: beojan  
> Created_at: 2021-03-08 09:46:29 UTC  
> Url: https://github.com/boostorg/phoenix/pull/105#issuecomment-792628035  

May I ask what's holding this up? If it's the Travis failure, I think that's because OS X 10.13 is no longer supported.   
From [here](https://docs.travis-ci.com/user/reference/osx/#macos-version) it looks like it can be fixed by adding `osx_image: xcode12.2` to the Mac OS job configurations.

---
