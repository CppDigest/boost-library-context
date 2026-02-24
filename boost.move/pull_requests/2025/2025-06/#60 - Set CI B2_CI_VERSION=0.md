# #60 Set CI B2_CI_VERSION=0 [Merged]

> Username: sdarwin  
> Created at: 2025-06-27 18:35:44 UTC  
> Updated at: 2025-08-23 21:04:15 UTC  
> Merged at: 2025-08-23 21:03:57 UTC  
> Closed at: 2025-08-23 21:03:57 UTC  
> Url: https://github.com/boostorg/move/pull/60  

Hi,  
  
Going back some years, boostorg/boost-ci provided a new way to configure B2 variables, which takes effect using  `B2_CI_VERSION=1`.    For example:    
  
(new) 'B2_LINKFLAGS': '-fuse-ld=gold'  
  
instead of  
  
(previous) 'B2_LINKFLAGS': 'linkflags=-fuse-ld=gold'  
  
Now that most boost libraries have migrated, we'd like to standardize boost-ci onto  `B2_CI_VERSION=1`.     
  
You can opt-out by configuring `B2_CI_VERSION=0` and then upgrade later.    
  
This pull request sets `B2_CI_VERSION=0`, so CI will continue to work, using the previous method.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2025-08-21 16:09:59 UTC  
> Url: https://github.com/boostorg/move/pull/60#issuecomment-3211247629  

Hi,  
It's been almost 2 months since opening this PR. After another month, upstream boost-ci will proceed to switch the default setting: B2_CI_VERSION=1. Please merge this PR to avoid broken CI tests. Thanks.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2025-08-23 21:04:15 UTC  
> Url: https://github.com/boostorg/move/pull/60#issuecomment-3217413706  

Thanks Sam for the patch and the reminder.

---
