# #70 support aix-soname=svr4 option, with gcc for now [Closed]

> Username: haubi  
> Created at: 2015-04-23 11:25:05 UTC  
> Updated at: 2021-10-02 22:19:08 UTC  
> Closed at: 2016-07-16 21:53:12 UTC  
> Url: https://github.com/boostorg/build/pull/70  

On AIX, using Import Files it is possible to provide filename-based shared library versioning similar to SVR4/ELF platforms, as outlined in  
https://gcc.gnu.org/install/configure.html#WithAixSoname  
  
While this patch already works (with gcc) so far, I'd like to improve it a little but fail with jam: In rule link.dll.aix I want to build the list of local object files ($sources of type OBJ) to extract the public symbols from. Currently I do this in the actions link.dll.aix in the shell code - works, but feels ugly.  
Hints welcome, thanks!

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-09-09 22:36:00 UTC  
> Url: https://github.com/boostorg/build/pull/70#issuecomment-139063947  

All PRs need to be made against the 'develop' branch and not the 'master' branch, as you have done.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2016-07-16 21:53:12 UTC  
> Url: https://github.com/boostorg/build/pull/70#issuecomment-233152978  

Closing in expectation that we will get a PR against develop.

---
