# #4 [winrt support] Replacing banned APIs Sleep and InitializeCriticalSection [Merged]

> Username: stgates  
> Created at: 2014-06-11 05:17:15 UTC  
> Updated at: 2014-06-24 21:50:18 UTC  
> Merged at: 2014-06-11 19:12:25 UTC  
> Closed at: 2014-06-11 19:12:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4  

Updated InitializeCriticalSection to InitializeCriticalSectionEx if on a new enough platform that supports.  
  
Replaced Sleep in the yield function to use std::this_thread::yield if targeting the Windows Runtime.

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-06-11 09:57:57 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#issuecomment-45722750  

`BOOST_USE_WINAPI_VERSION` and `BOOST_WINAPI_VERSION_VISTA` will generally not be defined in `lwm_win32_cs.hpp`, so I think we should use `BOOST_PLAT_WINDOWS_RUNTIME` there as well.  
  
In addition, the `Sleep` changes implicitly assume that `BOOST_PLAT_WINDOWS_RUNTIME == !BOOST_PLAT_WINDOWS_DESKTOP`, and I'd rather not depend on that, so uses of `BOOST_PLAT_WINDOWS_DESKTOP` should probably be replaced with `!BOOST_PLAT_WINDOWS_RUNTIME`.

---

## Comment 2

> Username: stgates  
> Created_at: 2014-06-11 18:34:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#issuecomment-45781329  

Ok I updated for the changes based on your feedback.  
  
Thanks,  
Steve

---

## Comment 3

> Username: glenfe  
> Created_at: 2014-06-11 18:36:48 UTC  
> Updated_at: 2014-06-11 18:44:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#discussion_r13666008  

#if defined(BOOST_PLAT_WINDOWS_RUNTIME)

---

## Comment 4

> Username: glenfe  
> Created_at: 2014-06-11 18:37:16 UTC  
> Updated_at: 2014-06-11 18:44:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#discussion_r13666048  

&& !defined(BOOST_PLAT_WINDOWS_RUNTIME)

---

## Comment 5

> Username: glenfe  
> Created_at: 2014-06-11 18:38:01 UTC  
> Updated_at: 2014-06-11 18:44:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#discussion_r13666090  

#if !defined(BOOST_PLAT_WINDOWS_RUNTIME)

---

## Comment 6

> Username: pdimov  
> Created_at: 2014-06-11 18:38:51 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#issuecomment-45781951  

An `#include <boost/predef.h>` for `BOOST_PLAT_WINDOWS_RUNTIME` appears to be missing in the first file, otherwise looks good to go.

---

## Comment 7

> Username: stgates  
> Created_at: 2014-06-11 18:38:55 UTC  
> Updated_at: 2014-06-11 18:44:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#discussion_r13666140  

The BOOST_PLAT_WINDOWS_RUNTIME macro is always going to be defined, that is how the Boost.Predefs work. It will just either have a true or false value.  
  
http://www.boost.org/doc/libs/1_55_0/libs/predef/doc/html/predef/using_the_predefs.html

---

## Comment 8

> Username: pdimov  
> Created_at: 2014-06-11 18:41:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#issuecomment-45782416  

I think that Predef always defines the macros, so `BOOST_PLAT_WINDOWS_RUNTIME` will be defined to 0 when not on WinRT. `#if` should be fine, and preferable, because it will warn on e.g. the missing `#include` or a misspelling.

---

## Comment 9

> Username: stgates  
> Created_at: 2014-06-11 18:43:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#issuecomment-45782668  

Correct, Predefs are always defined. This helps avoiding having to have ifdef checks always before.  
  
From: Peter Dimov [mailto:notifications@github.com]  
Sent: Wednesday, June 11, 2014 11:42 AM  
To: boostorg/smart_ptr  
Cc: Steve Gates  
Subject: Re: [smart_ptr] [winrt support] Replacing banned APIs Sleep and InitializeCriticalSection (#4)  
  
I think that Predef always defines the macros, so BOOST_PLAT_WINDOWS_RUNTIME will be defined to 0 when not on WinRT. #if should be fine, and preferable, because it will warn on e.g. the missing #include or a misspelling.  
  
—  
Reply to this email directly or view it on GitHubhttps://github.com/boostorg/smart_ptr/pull/4#issuecomment-45782416.

---

## Comment 10

> Username: stgates  
> Created_at: 2014-06-11 18:45:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/4#issuecomment-45782883  

Updated for missing predef.h include.

---
