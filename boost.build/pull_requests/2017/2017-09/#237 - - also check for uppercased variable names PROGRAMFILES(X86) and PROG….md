# #237 - also check for uppercased variable names PROGRAMFILES(X86) and PROG… [Closed]

> Username: IceCreamWarrior  
> Created at: 2017-09-11 15:52:16 UTC  
> Updated at: 2021-10-02 22:08:34 UTC  
> Closed at: 2017-09-11 15:58:32 UTC  
> Url: https://github.com/boostorg/build/pull/237  

…RAMFILES.  
  
This increases robustness and interoperability with third-party tools.  
Some tools only provide environments with forcibly upper-cased  
environment variable names (notably, Qt Creator). Running b2 under  
these tools will otherwise fail.  
  
Perhaps a better approach would be to support case-insensitive  
environment variables in b2, but that isn't trivial to do.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2017-09-11 15:58:32 UTC  
> Url: https://github.com/boostorg/build/pull/237#issuecomment-328575011  

This is already addressed globally by this https://github.com/boostorg/build/commit/9821622630035c0d7c3f4d7c196f847a335da2b6 and this https://github.com/boostorg/build/commit/b947347d6c8a463861ac604ccbaa2ba190cacf6e

---
