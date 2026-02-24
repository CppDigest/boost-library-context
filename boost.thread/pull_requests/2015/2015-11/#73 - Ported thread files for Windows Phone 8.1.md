# #73 Ported thread files for Windows Phone 8.1 [Closed]

> Username: mosherubin  
> Created at: 2015-11-03 19:23:58 UTC  
> Updated at: 2015-11-04 12:03:01 UTC  
> Closed at: 2015-11-04 11:33:06 UTC  
> Url: https://github.com/boostorg/thread/pull/73  

Only thread_primitives.hpp needed to be ported for Windows Phone 8.1.

---

## Comment 1

> Username: mosherubin  
> Created_at: 2015-11-04 10:27:02 UTC  
> Url: https://github.com/boostorg/thread/pull/73#issuecomment-153673681  

@viboes OK, I've added CreateSemaphoreExW to the win32 namespace.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-11-04 10:43:20 UTC  
> Updated_at: 2015-11-04 10:43:32 UTC  
> Url: https://github.com/boostorg/thread/pull/73#issuecomment-153680839  

Boost.Thread should already be working with Windows Phone 8.1 as of Boost 1.59. Perhaps you're not defining BOOST_NO_ANSI_APIS (now defined automatically with https://github.com/boostorg/config/commit/ad3141cebdef5271d00d49cd993f86a2a81a0ac9) and BOOST_USE_WINAPI_VERSION/_WIN32_WINNT correctly? All the declarations added for BOOST_PLAT_WINDOWS_RUNTIME are already there for BOOST_NO_ANSI_APIS and BOOST_USE_WINAPI_VERSION >= BOOST_WINAPI_VERSION_VISTA.

---

## Comment 3

> Username: mosherubin  
> Created_at: 2015-11-04 11:12:57 UTC  
> Updated_at: 2015-11-04 11:18:38 UTC  
> Url: https://github.com/boostorg/thread/pull/73#issuecomment-153688564  

@MarcelRaad You are right!  I restored the original 1.59 thread folder, sources, and headers and compiled -- the compilation succeeded.  We should therefore delete my pull request.  
  
I'm a Git newbie: do you delete it or do I?  If so, how?  Or is it only close?

---

## Comment 4

> Username: mosherubin  
> Created_at: 2015-11-04 11:33:06 UTC  
> Url: https://github.com/boostorg/thread/pull/73#issuecomment-153693939  

Based on MarcelRaad's comment, there is no need for my pull request.  Although my changes were needed for 1.57, the current 1.59 compiles well without my changes.  I am therefore closing it as unnecessary.

---

## Comment 5

> Username: viboes  
> Created_at: 2015-11-04 12:03:00 UTC  
> Url: https://github.com/boostorg/thread/pull/73#issuecomment-153700388  

Great :)

---
