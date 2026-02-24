# #257 Fix CI [Merged]

> Username: Kojoley  
> Created at: 2024-03-24 16:12:22 UTC  
> Updated at: 2024-08-28 10:12:32 UTC  
> Merged at: 2024-08-28 10:12:32 UTC  
> Closed at: 2024-08-28 10:12:32 UTC  
> Url: https://github.com/boostorg/context/pull/257  

* GHA ubuntu-18.04 image is no longer available -> switch to docker container.  
* arm64 multiarch docker container contains outdated sign keys because their deployment CI is broken -> switch to official debian docker containers where possible.

---
