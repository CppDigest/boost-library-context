# #48 Include C++ headers instead of C variants [Merged]

> Username: Flamefire  
> Created at: 2019-12-13 20:52:36 UTC  
> Updated at: 2019-12-14 12:35:42 UTC  
> Merged at: 2019-12-14 12:35:38 UTC  
> Closed at: 2019-12-14 12:35:39 UTC  
> Url: https://github.com/boostorg/nowide/pull/48  

Supersedes https://github.com/boostorg/nowide/pull/40  
  
While technically for e.g. `_wfopen` one needs `stdio.h` all compilers/stdlibs having this extension also have it when including `<cstdio>`. So use the C++ headers and the C++ functions to be consistent

---
