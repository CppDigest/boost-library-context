# #56 Fix issues found by new CI jobs: misalignments between Windows API and winapi [Closed]

> Username: jeking3  
> Created at: 2017-09-16 13:11:16 UTC  
> Updated at: 2017-09-16 16:30:40 UTC  
> Closed at: 2017-09-16 16:27:03 UTC  
> Url: https://github.com/boostorg/winapi/pull/56  

Resolves incompatibilities with _WIN32_WINNT=0x0400 and 0x0A00 with the desktop family.  0x0501 was resolved with separate commits.  
  
Results of Appveyor CI build:  https://ci.appveyor.com/project/jeking3/winapi/build/1.0.22-develop  
Results of Travis CI build: https://travis-ci.org/jeking3/winapi/builds/276282135  
  
This fixes #50  
This fixes #52

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2017-09-16 14:48:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/winapi/pull/56#pullrequestreview-63204538  

📁 include/boost/detail/winapi/jobs.hpp

```diff
  18 |+ #if (BOOST_USE_WINAPI_VERSION >= BOOST_WINAPI_VERSION_WINXP)
  19 |+ 
  20 |+ #if (BOOST_USE_WINDOWS_H)
```

> Username: Lastique  
> Created_at: 2017-09-16 14:28:31 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139287784  

`BOOST_USE_WINDOWS_H` is supposed to be tested for being defined or not.

> Username: jeking3  
> Created_at: 2017-09-16 15:09:51 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288591  

Thanks for catching this.  I will fix it in the next push.

---

📁 include/boost/detail/winapi/jobs.hpp

```diff
  21 |+ // IsProcessInJob is defined in jobapi.h in the Windows 8 SDK or later
  22 |+ #if (BOOST_WINAPI_WINDOWS_SDK >= BOOST_WINAPI_WINDOWS_SDK_8)
  23 |+ #include <jobapi.h>
```

> Username: Lastique  
> Created_at: 2017-09-16 14:37:05 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139287972  

Why are these includes needed? Both jobapi.h and jobapi2.h are included from WinBase.h, which is included from windows.h.

> Username: jeking3  
> Created_at: 2017-09-16 15:14:04 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288684  

The Microsoft documentation for IsProcessInJob (https://msdn.microsoft.com/en-us/library/windows/desktop/ms684127(v=vs.85).aspx) indicates this is in a different header for certain SDK revisions, but I see it in winbase up through the latest SDK 10 so I will remove these sections.

---

📁 include/boost/detail/winapi/jobs.hpp

```diff
  16 |+ // in Windows XP / Windows 2003 or later
  17 |+ 
  18 |+ #if (BOOST_USE_WINAPI_VERSION >= BOOST_WINAPI_VERSION_WINXP)
```

> Username: Lastique  
> Created_at: 2017-09-16 14:38:54 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288009  

I don't think this is correct. Some functions, e.g. `CreateJobObjectA`, are available since Win2k.

> Username: jeking3  
> Created_at: 2017-09-16 15:09:26 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288584  

The CI build proved otherwise, failing when _WIN32_WINNT=0x0500.  
  
This is further proven by the documentation for CreateJobObject at https://msdn.microsoft.com/en-us/library/windows/desktop/ms682409(v=vs.85).aspx which specifically states:   
  
Minimum supported client | Windows XP [desktop apps only]  
-- | --

> Username: Lastique  
> Created_at: 2017-09-16 15:15:02 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288706  

MSDN cannot be trusted as it doesn't mention systems prior to XP. Not consistently, anyway. I can see `CreateJobObjectA` defined in C:\Program Files (x86)\Windows Kits\10\Include\10.0.15063.0\um\WinBase.h:7563, guarded by `#if (_WIN32_WINNT >= 0x0500)` a few lines above.

> Username: jeking3  
> Created_at: 2017-09-16 15:24:38 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288893  

I see that as well, so I will fix this up.

---

📁 include/boost/detail/winapi/jobs.hpp

```diff
 102 | {
  94 |-     return ::CreateJobObjectA(reinterpret_cast< ::_SECURITY_ATTRIBUTES* >(lpJobAttributes), lpName);
 103 |+     return ::CreateJobObjectA(winapi::detail::cast_ptr(lpJobAttributes), lpName);
```

> Username: Lastique  
> Created_at: 2017-09-16 14:39:51 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288031  

What are these changes for? `cast_ptr` is not intended to be a replacement for `reinterpret_cast`.

> Username: jeking3  
> Created_at: 2017-09-16 15:14:47 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288702  

I can remove them.  I was trying to normalize on patterns I saw elsewhere.


📁 include/boost/detail/winapi/config.hpp

```diff
  91 |+ #define BOOST_USE_WINAPI_FAMILY BOOST_WINAPI_FAMILY_DESKTOP_APP
  92 |+ #endif
  93 |+ #endif
```

> Username: Lastique  
> Created_at: 2017-09-16 14:47:17 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288161  

Maybe it's better to include winapifamily.h instead of defining constants depending on the particular SDK version.

> Username: jeking3  
> Created_at: 2017-09-16 15:15:52 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139288725  

We can only include winapifamily.h if BOOST_USE_WINDOWS_H is defined, correct?  Further, it can only be included in MinGW-w64 and if the Platform SDK is 8.0 or later.  I actually have this coded up already locally for changes related to boost random so I will pull it back into this.

> Username: Lastique  
> Created_at: 2017-09-16 15:38:55 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139289180  

If we really need the family macros then I think it's ok to include winapifamily.h even when `BOOST_USE_WINDOWS_H` is not defined. That header is fairly lightweight and doesn't define any offending macros, so it should be pretty safe.

> Username: jeking3  
> Created_at: 2017-09-16 15:40:51 UTC  
> Updated_at: 2017-09-16 16:00:55 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139289218  

Okay I was trying to avoid that, but I'll simplify.  I struggled with the same decision but I decided initially to follow the pattern in the project.


---

## Comment 2

> Username: Lastique  
> Created_at: 2017-09-16 15:37:34 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#issuecomment-329975861  

I've incorporated some of the fixes into develop.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-09-16 15:53:16 UTC  
> Updated_at: 2017-09-16 16:00:18 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#issuecomment-329976804  

I see that.  I believe you've fixed #51.  The changes in jobs.hpp will fix #50 and in config.hpp will fix #52.

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-09-16 16:08:36 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#issuecomment-329977733  

@Lastique If you pull the jobs.hpp changes into develop, I'm not certain any of the config.hpp changes are needed (yet), however it would be nice to get these in if they result in a clean CI build (except for the non-desktop family job at the end, which is #53.

---

## Review 5 [Commented]

> Username: jeking3  
> Created_at: 2017-09-16 16:09:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/winapi/pull/56#pullrequestreview-63206628  

📁 include/boost/detail/winapi/config.hpp

```diff
  90 |+ #else
  91 |+ // If none is specified, default to a desktop application which is the most
  92 |+ // backwards compatible to previos ways of doing things.
```

> Username: jeking3  
> Created_at: 2017-09-16 16:09:13 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#discussion_r139289740  

ugh, a typo


---

## Comment 6

> Username: Lastique  
> Created_at: 2017-09-16 16:21:57 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#issuecomment-329978513  

I've pulled the fix for jobs.hpp to develop. We can focus on the non-desktop stuff now.  
  
PS: The AppVeyor CI is not working currently for some reason, so I'll be fiddling with it trying to trigger with occasional commits. Sorry if that causes inconvenience.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-09-16 16:26:52 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#issuecomment-329978824  

I'm running my own CI builds by submitting a pull request into my own develop. :)

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-09-16 16:30:40 UTC  
> Url: https://github.com/boostorg/winapi/pull/56#issuecomment-329979129  

I'll submit a bcrypt.hpp pull request which may or may not include changes to config.hpp when I have something that works in random against it.

---
