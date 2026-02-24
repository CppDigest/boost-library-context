# #185 Set CI B2_CI_VERSION=0 [Open]

> Username: sdarwin  
> Created at: 2025-06-27 18:24:36 UTC  
> Updated at: 2025-08-21 16:06:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/185  

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
> Created_at: 2025-08-21 16:06:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/185#issuecomment-3211235775  

Hi,  
It's been almost 2 months since opening this PR. After another month, upstream boost-ci will proceed to switch the default setting: `B2_CI_VERSION=1`. Please merge this PR to avoid broken CI tests. Thanks.

---
