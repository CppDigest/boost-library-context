# #162 Fixed setting of SONAME for toolset qcc [Merged]

> Username: NiklasAngare  
> Created at: 2017-02-11 22:31:16 UTC  
> Updated at: 2021-10-02 22:18:54 UTC  
> Merged at: 2017-02-12 21:46:57 UTC  
> Closed at: 2017-02-12 21:46:57 UTC  
> Url: https://github.com/boostorg/build/pull/162  

The SONAME is now set unconditionally. The use if HAVE_SONAME was probably mistakenly copied from gcc.jam.  
  
Without this, dlls didn't work unless they were in the same path they were compiled. Fixes #85 and https://svn.boost.org/trac/boost/ticket/10227.  
  
My test runner NA-QNX660-x86 is currently running with this fix.

---
