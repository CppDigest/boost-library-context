# #28 Fix Visual Studio 2010 error C2440: 'initializing' : cannot convert from... [Closed]

> Username: pianofab  
> Created at: 2014-10-21 20:38:43 UTC  
> Updated at: 2014-11-03 20:58:37 UTC  
> Closed at: 2014-11-03 17:07:57 UTC  
> Url: https://github.com/boostorg/thread/pull/28  

... 'BOOL (__stdcall *)(HINSTANCE,DWORD,LPVOID)' to 'BOOL (__stdcall *const )(HANDLE,DWORD,LPVOID)' This conversion requires a reinterpret_cast, a C-style cast or function-style cast

---

## Comment 1

> Username: ned14  
> Created_at: 2014-10-22 23:17:01 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-60171059  

I am surprised we haven't see this build failure before - we obviously test regularly on VS2010. Is this a new failure? Have you tested develop branch? Are you using special build options?

---

## Comment 2

> Username: pianofab  
> Created_at: 2014-10-23 01:44:04 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-60181991  

I was surprised too! I wonder if this header is ever included by anyone in the boost test build, since it is designed to work around a specific issue with MFC in some specific use cases.

---

## Comment 3

> Username: pianofab  
> Created_at: 2014-10-23 01:49:23 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-60182371  

I should also mention that this file is relatively new (probably added with boost 1.54).

---

## Comment 4

> Username: pianofab  
> Created_at: 2014-10-23 01:58:02 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-60182858  

To clarify even more, in my environment HANDLE and HINSTANCE are not interchangeable:  
Find Definition (F12) takes me to:  
  
WinNT.h:  
#ifdef STRICT  
typedef void *HANDLE;  
  
windef.h:  
DECLARE_HANDLE(HINSTANCE);  
  
This last one is equivalent to:  
typedef HINSTANCE__ *HINSTANCE;  
  
Which explains why the code does not compile.

---

## Comment 5

> Username: ghost  
> Created_at: 2014-10-29 01:40:26 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-60861299  

![2014_boost_bug_issue_thread_mfc_dll](https://cloud.githubusercontent.com/assets/8745082/4819891/883fd1ca-5f0b-11e4-8093-8af043e1a2a7.png)  
Thanks,I will check this patch.Some error occurs in my CAED project when I use MFC-Dlls work with static linked boost-threads .The main application.exe  can not load the the local MFC DLL which using the `boost::thread_specific_ptr`.  
![2014_boost_bug_issue_thread_mfc_dll_load_failed](https://cloud.githubusercontent.com/assets/8745082/4819950/894c5c68-5f0c-11e4-80f3-34311c571dce.png)

---

## Comment 6

> Username: viboes  
> Created_at: 2014-11-03 18:07:31 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-61520734  

Why have you closed this issue?

---

## Comment 7

> Username: pianofab  
> Created_at: 2014-11-03 20:51:00 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-61546206  

Since there was the separate trac ticket, I wanted to avoid this patch to be integrated by mistake since the patch (per discussion) needs to be different.

---

## Comment 8

> Username: viboes  
> Created_at: 2014-11-03 20:58:37 UTC  
> Url: https://github.com/boostorg/thread/pull/28#issuecomment-61547301  

Thanks for clarification.

---
