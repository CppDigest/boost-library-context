# #629 Aix lib [Closed]

> Username: EGuesnet  
> Created at: 2020-06-23 15:34:15 UTC  
> Updated at: 2021-10-02 20:59:22 UTC  
> Closed at: 2021-06-11 01:54:39 UTC  
> Url: https://github.com/boostorg/build/pull/629  

Hello,  
  
OS: AIX 7.1  
Compiler: GCC 8.4  
  
I am building Boost on AIX. Our goal is to distribute it through website for OpenSource software, like http://www.bullfreeware.com/ or https://www.ibm.com/support/pages/node/882892.  
Up to now, Boost Build produces libraries as .so on AIX. It is not the normal way on AIX.  
  
A shared library must be distributed as an archive (.a) containing a shared object (typically .so or .so.version).  
I have a proof of work to do this. However, I fade some limitation, and I want to know how to do this in a better way.  
  
With my modifications, the real target is the archive, and shared object are intermediate object. It is needed because only the real target is installed.  
I use shell substitution to produce the right object from the target name as Boost Build tools (:S= and so on) seem not enough.  
  
My troubles are the following:  
- First, shared and static libraries cannot have the same prefix. On AIX, both are .a files. I have decided static libraries are .static.a, but it is a poor workaround.  
- Second, I have modified the way a shared library is created to produce an archive immediately after (except for plugin, dlopened liraries). I do not found a way to apply this only on AIX.  
- Third, the .so might be versioned, but not the .a. Eg, libbboost_foo.a contains libboost_foo.so.1.69.0. If I add a version to the target, the .a file will have a version (we do not want). Without version to the target, .so does not have. With .so as a target and .a produced additionally, .a is not installed.  
  
If you has any idea to create shared libraries as archive on AIX with a better way, I am interested in.

---

## Comment 1

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:44 UTC  
> Url: https://github.com/boostorg/build/pull/629#issuecomment-850859477  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:21 UTC  
> Url: https://github.com/boostorg/build/pull/629#issuecomment-932819631  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
