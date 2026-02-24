# #50 Fixed bug Trac #13038 [Merged]

> Username: bmwiedemann  
> Created at: 2017-05-22 08:48:00 UTC  
> Updated at: 2017-05-24 21:06:57 UTC  
> Merged at: 2017-05-24 21:06:41 UTC  
> Closed at: 2017-05-24 21:06:41 UTC  
> Url: https://github.com/boostorg/container/pull/50  

Have constant link order  
to make builds reproducible.  
See https://reproducible-builds.org/ for why this is good.  
  
Without this patch g++ would order functions in libboost_container.so.1.64.0  
depending on random order of files in the build system's filesystem.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-05-24 21:06:57 UTC  
> Url: https://github.com/boostorg/container/pull/50#issuecomment-303851882  

Thanks for the patch

---
