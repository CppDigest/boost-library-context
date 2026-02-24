# #332 boost/build: fix ch declaration in debugger.c [Merged]

> Username: mksully22  
> Created at: 2018-08-20 11:36:58 UTC  
> Updated at: 2021-10-02 22:08:11 UTC  
> Merged at: 2018-08-21 17:21:05 UTC  
> Closed at: 2018-08-21 17:21:05 UTC  
> Url: https://github.com/boostorg/build/pull/332  

fix declaration of ch variable in debug_parent_wait() to be an int rather than a char. fgetc does return an int and if return variable is declared to be a char truncation occurs that results in later comparisons of ch to EOF to fail (0xff compare to 0xffffffff) on ppc64le.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-08-21 17:10:07 UTC  
> Url: https://github.com/boostorg/build/pull/332#issuecomment-414750276  

AMDG  
  
Thanks!  I noticed the failures in the regression  
matrix before, but I couldn't track down the  
problem.  
  
In Christ,  
Steven Watanabe

---
