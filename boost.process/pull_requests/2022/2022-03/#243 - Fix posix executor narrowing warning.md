# #243 Fix posix executor narrowing warning [Closed]

> Username: arghness  
> Created at: 2022-03-23 21:06:43 UTC  
> Updated at: 2022-06-09 10:40:33 UTC  
> Closed at: 2022-06-09 10:40:32 UTC  
> Url: https://github.com/boostorg/process/pull/243  

Fixes #235   
  
Tested with gcc 11.2.1.

---

## Comment 1

> Username: arghness  
> Created_at: 2022-03-24 09:54:20 UTC  
> Updated_at: 2022-03-24 09:54:59 UTC  
> Url: https://github.com/boostorg/process/pull/243#issuecomment-1077441871  

I'm a bit confused about the branching here -- I've submitted to merge in to master, as master seems to have the latest updates to this file and seems to be closest to the Boost 1.78 release, but other pull requests seem to be submitted to the default develop branch, which seems really out of date for include/boost/process/detail/posix/executor.hpp .

---

## Comment 2

> Username: arghness  
> Created_at: 2022-06-09 10:40:32 UTC  
> Url: https://github.com/boostorg/process/pull/243#issuecomment-1150961266  

An alternate fix was made in commit ab82e78c3db2b472356642657500f7a4b5a2857e

---
