# #4 ADD Create*Ex SUPPORT FOR SYNCHRONIZATION PRIMITIVES [Closed]

> Username: BurningEnlightenment  
> Created at: 2015-06-26 20:33:10 UTC  
> Updated at: 2015-06-28 17:29:00 UTC  
> Closed at: 2015-06-28 17:29:00 UTC  
> Url: https://github.com/boostorg/winapi/pull/4  

Add CreateMutexEx, CreateSemaphoreEx and CreateEventEx function  
declarations and define related symbols.  
  
Force the related API wrapper to use the new APIs when compiling for  
the Windows Runtime platform.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-06-28 10:28:42 UTC  
> Url: https://github.com/boostorg/winapi/pull/4#issuecomment-116252460  

There are missing Boost.Predef includes in the changed files. Also, why did you remove BOOST_NO_ANSI_APIS use?

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-06-28 10:35:03 UTC  
> Url: https://github.com/boostorg/winapi/pull/4#issuecomment-116253039  

I've fixed a bug in the CreateMutexW signature in develop, so you may want to rebase the PR.

---
