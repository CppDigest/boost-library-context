# #25 Expand the CI matrix [Merged]

> Username: jeking3  
> Created at: 2018-10-14 16:34:02 UTC  
> Updated at: 2018-10-15 00:37:34 UTC  
> Merged at: 2018-10-15 00:37:29 UTC  
> Closed at: 2018-10-15 00:37:29 UTC  
> Url: https://github.com/boostorg/assign/pull/25  

This expands the CI test matrix on linux to check many more configurations for regressions, based on recent discussions and changes that broke older compilers.  The libc++ / osx job is disabled because of a strange rvm behavior on those build systems (they take over basic commands like "cd").  Not even sure why "rvm" is getting invoked on osx.

---
