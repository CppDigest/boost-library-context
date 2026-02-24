# #127 Set CI B2_CI_VERSION=0 [Open]

> Username: sdarwin  
> Created at: 2025-06-27 18:28:46 UTC  
> Updated at: 2025-06-30 01:55:18 UTC  
> Url: https://github.com/boostorg/algorithm/pull/127  

Hi,  
  
Going back some years, boostorg/boost-ci provided a new way to configure B2 variables, which takes effect using B2_CI_VERSION=1. For example:  
  
(new) 'B2_LINKFLAGS': '-fuse-ld=gold'  
  
instead of  
  
(previous) 'B2_LINKFLAGS': 'linkflags=-fuse-ld=gold'  
  
Now that most boost libraries have migrated, we'd like to standardize boost-ci onto B2_CI_VERSION=1.  
  
You can opt-out by configuring B2_CI_VERSION=0 and then upgrade later.  
  
This pull request sets B2_CI_VERSION=0, so CI will continue to work, using the previous method.

---

## Comment 1

> Username: jeking3  
> Created_at: 2025-06-29 20:31:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/127#issuecomment-3017079660  

I don't it matters much here since this repo does not have appveyor enabled at the moment.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2025-06-30 01:55:18 UTC  
> Url: https://github.com/boostorg/algorithm/pull/127#issuecomment-3017495935  

> does not have appveyor enabled  
  
To be thorough, the update could be merged anyway. for future use.     
Or delete `appveyor.yml` so that it's clear appveyor isn't enabled.

---
