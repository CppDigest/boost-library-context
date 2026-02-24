# #4 Fixing break on cygwin because local_free_on_destruction.hpp is not included. Ticket #10137. [Merged]

> Username: stgates  
> Created at: 2014-06-20 03:41:41 UTC  
> Updated at: 2014-07-30 19:12:15 UTC  
> Merged at: 2014-07-30 19:11:17 UTC  
> Closed at: 2014-07-30 19:11:17 UTC  
> Url: https://github.com/boostorg/system/pull/4  

Cleans up the system_error_category::message function a bit to not have so many different ifdef paths. Now it is either using FormatMessageW on CE, WinRT, or if BOOST_NO_ANSI_APIS is defined. Otherwise FormatMessageA is used.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2014-06-20 23:31:06 UTC  
> Url: https://github.com/boostorg/system/pull/4#issuecomment-46736745  

I can confirm that the breakage on cygwin is gone. I'm concerned about the code that got dropped on the floor, though. I see that code was added in commit [c64f27c]. We should either keep that code, or else contact @chriskohlhoff to find out what problem he was trying to deal with when he wrote it.

---

## Comment 2

> Username: stgates  
> Created_at: 2014-06-20 23:35:55 UTC  
> Url: https://github.com/boostorg/system/pull/4#issuecomment-46736990  

I'll try to contact @chriskohlhoff, I figured it was better to have less code instead of 3 different branches.

---

## Comment 3

> Username: ericniebler  
> Created_at: 2014-06-20 23:36:14 UTC  
> Url: https://github.com/boostorg/system/pull/4#discussion_r14045848  

Where'd this code go?

---

## Comment 4

> Username: ericniebler  
> Created_at: 2014-06-20 23:37:42 UTC  
> Url: https://github.com/boostorg/system/pull/4#issuecomment-46737051  

Good. He's a knowledgeable dev. I'd think twice before deleting his code.

---

## Comment 5

> Username: stgates  
> Created_at: 2014-06-20 23:53:45 UTC  
> Url: https://github.com/boostorg/system/pull/4#issuecomment-46737684  

To keep it simple and quick, I'll just update it include his path as well. I was maybe jumping the gun and should just deal with the fix and not bother with cleaning up at this time.

---

## Comment 6

> Username: chriskohlhoff  
> Created_at: 2014-06-21 00:13:35 UTC  
> Url: https://github.com/boostorg/system/pull/4#issuecomment-46738546  

I'll take a closer look later, but I think the two-variant version is best, i.e.:  
- FormatMessageW without LocalFree  
- FormatMessageA with LocalFree  
  
Originally I was unaware that Windows Phone didn't allow FormatMessageA, so I added the separate code branch for Windows Runtime to minimise risk of breakage on existing platforms.

---

## Comment 7

> Username: stgates  
> Created_at: 2014-06-21 00:19:59 UTC  
> Url: https://github.com/boostorg/system/pull/4#issuecomment-46738799  

I think the two-variant is best, but just in case I've implemented the 3 one as well. I didn't add it to this pull request but it can be located in the foxforcygwin2 branch in my repository:  
  
https://github.com/MSOpenTech/system/commit/1ab811c8986dccfd6cbbb0f22a1cb0da1f9374d9  
  
Let me know if I can do anything else to help.  
Steve

---
