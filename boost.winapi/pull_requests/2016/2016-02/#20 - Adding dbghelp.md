# #20 Adding dbghelp [Closed]

> Username: jlodos  
> Created at: 2016-02-13 21:37:47 UTC  
> Updated at: 2016-02-14 01:42:43 UTC  
> Closed at: 2016-02-14 01:13:01 UTC  
> Url: https://github.com/boostorg/winapi/pull/20  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-02-13 23:03:05 UTC  
> Url: https://github.com/boostorg/winapi/pull/20#issuecomment-183769877  

@jlodos Is there interest from your side to have this header somewhere outside of boost.dll ?

---

## Comment 2

> Username: jlodos  
> Created_at: 2016-02-13 23:16:56 UTC  
> Url: https://github.com/boostorg/winapi/pull/20#issuecomment-183770502  

@klemens-morgenstern  I am not using it, just trying to help. I do think that it belongs to winapi.

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-02-13 23:26:10 UTC  
> Url: https://github.com/boostorg/winapi/pull/20#issuecomment-183770883  

First, please, separate the changes for dbghelp from the other changes.  
  
Second, my concern regarding dbghelp is that it seems an optional development library rather than part of Windows API.

---

## Comment 4

> Username: jlodos  
> Created_at: 2016-02-13 23:39:07 UTC  
> Url: https://github.com/boostorg/winapi/pull/20#issuecomment-183771662  

I am not sure of the definition of "Windows API". I agree that there are several different APIs distributed together in the Windows SDK. To me any API from the SDK that forces the user to include windows.h could have its place in winapi. Isn´t that its purpose?

---

## Comment 5

> Username: Lastique  
> Created_at: 2016-02-13 23:46:46 UTC  
> Url: https://github.com/boostorg/winapi/pull/20#issuecomment-183772925  

It is. But dbghelp.h is not a part of windows .h.  
  
But you're right that it's distributed in Windows SDK. I can see it is also present in MinGW-w64 but not MinGW. So I guess we could add it, with all contents conditioned on `!defined(BOOST_WINAPI_IS_MINGW)`.  
  
Also, I think you closed the other PR by mistake.

---

## Comment 6

> Username: Lastique  
> Created_at: 2016-02-14 01:13:01 UTC  
> Url: https://github.com/boostorg/winapi/pull/20#issuecomment-183783711  

I'm closing this PR as it includes some of the changes that were merged and then modified. Please, rebase for the current develop and only include changes needed for dbghelp.

---
