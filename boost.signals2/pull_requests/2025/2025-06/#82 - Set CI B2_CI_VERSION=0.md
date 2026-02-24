# #82 Set CI B2_CI_VERSION=0 [Merged]

> Username: sdarwin  
> Created at: 2025-06-27 18:32:06 UTC  
> Updated at: 2025-08-24 20:00:54 UTC  
> Merged at: 2025-08-24 20:00:54 UTC  
> Closed at: 2025-08-24 20:00:54 UTC  
> Url: https://github.com/boostorg/signals2/pull/82  

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

> Username: sdarwin  
> Created_at: 2025-08-21 16:09:25 UTC  
> Url: https://github.com/boostorg/signals2/pull/82#issuecomment-3211245025  

Hi,  
It's been almost 2 months since opening this PR. After another month, upstream boost-ci will proceed to switch the default setting: B2_CI_VERSION=1. Please merge this PR to avoid broken CI tests. Thanks.

---
