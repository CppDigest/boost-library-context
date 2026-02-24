# #5 ADD Create*Ex SUPPORT FOR SYNCHRONIZATION PRIMITIVES [Closed]

> Username: BurningEnlightenment  
> Created at: 2015-06-28 17:33:10 UTC  
> Updated at: 2015-06-29 19:49:13 UTC  
> Closed at: 2015-06-29 19:49:13 UTC  
> Url: https://github.com/boostorg/winapi/pull/5  

Add CreateMutexEx, CreateSemaphoreEx and CreateEventEx function  
declarations and define related symbols.  
  
Force the related API wrapper to use the new APIs when compiling for  
the Windows Runtime platform.

---

## Comment 1

> Username: BurningEnlightenment  
> Created_at: 2015-06-28 17:46:41 UTC  
> Updated_at: 2015-06-29 08:11:13 UTC  
> Url: https://github.com/boostorg/winapi/pull/5#issuecomment-116303481  

I removed the BOOST_NO_ANSI_APIS differentiation where we pass null pointers instead of (user supplied) string pointers, because it doesn't make any difference (except for removing some negligible overhead).  
Sorry for recreating the request (original #4).

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-06-29 19:49:13 UTC  
> Url: https://github.com/boostorg/winapi/pull/5#issuecomment-116815178  

Committed a slightly modified and fixed version of your change.

---
