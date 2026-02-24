# #51 - -fsplit-stack flag and AIX [Closed]

> Username: EGuesnet  
> Created at: 2020-06-23 10:37:52 UTC  
> Updated at: 2021-03-16 17:08:15 UTC  
> Closed at: 2021-03-16 17:08:15 UTC  
> Url: https://github.com/boostorg/coroutine/issues/51  

Hi,  
I am trying to port Boost on AIX.  
  
OS: AIX 7.1  
Compiler: GCC 8.4  
  
I have reported an issue about -fsplit-stack on context library. Fiber library is also concerned. I create this issue to inform you, but it will be easier if the discussion will be centralized at boostorg/context#143.  
  
TL;DR: the -fsplit-stack flag seems to be available only on Linux, and it is used for all OS by Boost.

---

## Comment 1

> Username: olk  
> Created at: 2020-06-26 05:21:12 UTC  
> Url: https://github.com/boostorg/coroutine/issues/51#issuecomment-649974793  

Could you provide the pull-request please?

---

## Comment 2

> Username: EGuesnet  
> Created at: 2020-06-26 12:39:43 UTC  
> Url: https://github.com/boostorg/coroutine/issues/51#issuecomment-650156953  

> Could you provide the pull-request please?  
  
Done for coroutine and fiber libraries.
