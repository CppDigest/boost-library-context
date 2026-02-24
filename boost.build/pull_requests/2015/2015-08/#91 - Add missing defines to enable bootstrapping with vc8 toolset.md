# #91 Add missing defines to enable bootstrapping with vc8 toolset. [Closed]

> Username: nomadbyte  
> Created at: 2015-08-20 05:58:30 UTC  
> Updated at: 2021-10-02 22:20:21 UTC  
> Closed at: 2015-09-03 11:17:08 UTC  
> Url: https://github.com/boostorg/build/pull/91  

Boost.Build supports bootstrapping and building with VisualStudio 2005 (`bootstrap vc8`). However  when actually attempting the bootstrap, it fails with compile errors:  
  
```  
builtins.c(1887) : error C2065: 'FSCTL_GET_REPARSE_POINT' : undeclared identifier  
builtins.c(1891) : error C2065: 'IO_REPARSE_TAG_SYMLINK' : undeclared identifier  
```  
  
This patch adds macros needed to properly define theses missing above in the VisualStudio 2005 context. In other contexts the changes should be transparent.  
The resulting code successfully completes the bootstrap with VisualStudio 2005 (on Win XP) and passes the `test_all.py` with the same results as the base Boost.Build `develop` revision.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-09-03 11:17:08 UTC  
> Url: https://github.com/boostorg/build/pull/91#issuecomment-137410711  

Cherry-picked, thanks!

---
