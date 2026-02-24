# #51 Respect BOOST_USE_WINDOWS_H again [Merged]

> Username: MarcelRaad  
> Created at: 2015-03-26 12:52:15 UTC  
> Updated at: 2015-04-03 16:15:03 UTC  
> Merged at: 2015-04-03 16:11:17 UTC  
> Closed at: 2015-04-03 16:11:17 UTC  
> Url: https://github.com/boostorg/thread/pull/51  

With the GetTickCount64 and WinRT fixes, the Windows API functions were always re-declared regardless of BOOST_USE_WINDOWS_H. This breaks clang-cl, which complains about conflicting definitions.  
  
I hope I didn't break the WinRT build, unfortunately I don't have Windows 8. Tested on Windows 7 with MSVC12, MSVC14, and clang-cl r233105 with and without BOOST_USE_WINDOWS_H.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-03-27 12:26:09 UTC  
> Url: https://github.com/boostorg/thread/pull/51#issuecomment-86926334  

Niall please, could you take a look at this issue?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-03-27 20:55:23 UTC  
> Url: https://github.com/boostorg/thread/pull/51#discussion_r27330385  

Oops, sorry, this #else was removed in the previous commit to this file. Of course I'll remove it again, together with addressing any other comments you may have.

---

## Comment 3

> Username: ned14  
> Created_at: 2015-04-03 16:11:12 UTC  
> Url: https://github.com/boostorg/thread/pull/51#issuecomment-89339350  

Looks okay. I'm going to merge it and fire up the CI to see if all builds.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2015-04-03 16:14:26 UTC  
> Url: https://github.com/boostorg/thread/pull/51#issuecomment-89340578  

Thank you!

---
