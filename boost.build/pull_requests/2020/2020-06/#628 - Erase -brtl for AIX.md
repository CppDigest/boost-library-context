# #628 Erase -brtl for AIX [Merged]

> Username: EGuesnet  
> Created at: 2020-06-23 15:26:05 UTC  
> Updated at: 2021-10-02 21:01:33 UTC  
> Merged at: 2020-09-17 01:04:34 UTC  
> Closed at: 2020-09-17 01:04:34 UTC  
> Url: https://github.com/boostorg/build/pull/628  

Hello,  
  
OS: AIX 7.1  
Compiler: GCC 8.4  
  
I am building Boost on AIX. Our goal is to distribute it throught website for OpenSource software, like http://www.bullfreeware.com/ or https://www.ibm.com/support/pages/node/882892.  
Up to now, Boost Build uses the -brtl flag.  
  
We do not want -brtl as this flag decreases performances, and boost do not need it. So, -brtl must be added by a project only if the project needs it, and not as a default by b2. All librarires tested works without -brtl.  
  
Without -brtl, AIX linker looks only for archive file .a containg shared libraries (typically .so or .so.version). It is the default and normal behavior. All software distributed on AIX must provide .a and not just .so. If a project needs .so and do not want to put it on an archive, the project can add -brtl, but it might not be added by default.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-09-14 03:16:29 UTC  
> Url: https://github.com/boostorg/build/pull/628#issuecomment-691787198  

Recycling for CI re-testing.

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:31 UTC  
> Url: https://github.com/boostorg/build/pull/628#issuecomment-932819930  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
