# #15 update lcov in .travis.yml [Merged]

> Username: sdarwin  
> Created at: 2020-12-23 23:53:59 UTC  
> Updated at: 2021-01-02 14:06:23 UTC  
> Merged at: 2021-01-02 14:06:14 UTC  
> Closed at: 2021-01-02 14:06:14 UTC  
> Url: https://github.com/boostorg/any/pull/15  

Although it seems that travis is passing, a few of the different jobs actually have errors in the log output.  
  
```  
geninfo: ERROR: need tool gcov-6!  
```  
and  
```  
lcov: ERROR: cannot read file /home/travis/build/boostorg/any/coverals/coverage.info!  
```  
  
The version of gcov shouldn't be hardcoded to gcov-6. It needs to match the version of the compiler, so sometimes gcov-8, etc.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-01-02 14:06:23 UTC  
> Url: https://github.com/boostorg/any/pull/15#issuecomment-753477760  

Many thanks!

---
