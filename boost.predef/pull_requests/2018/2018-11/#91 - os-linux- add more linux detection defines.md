# #91 os/linux: add more linux detection defines. [Merged]

> Username: xnox  
> Created at: 2018-11-26 12:39:02 UTC  
> Updated at: 2018-11-26 14:15:56 UTC  
> Merged at: 2018-11-26 14:15:56 UTC  
> Closed at: 2018-11-26 14:15:56 UTC  
> Url: https://github.com/boostorg/predef/pull/91  

Some releases of g++, on some platforms, whilst running under some  
standards, may not define neither linux, nor __linux. Add detections  
for __linux__ and __gnu_linux__ for robustness.

---
