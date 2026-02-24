# #361 Fix clang-win.jam so that it works [Merged]

> Username: pdimov  
> Created at: 2018-10-27 19:20:10 UTC  
> Updated at: 2021-10-02 21:00:02 UTC  
> Merged at: 2018-10-29 23:32:21 UTC  
> Closed at: 2018-10-29 23:32:21 UTC  
> Url: https://github.com/boostorg/build/pull/361  



---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-27 19:36:17 UTC  
> Url: https://github.com/boostorg/build/pull/361#issuecomment-433649221  

Fixes #330.

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-29 23:37:15 UTC  
> Url: https://github.com/boostorg/build/pull/361#issuecomment-434119632  

With this in place, clang-cl should now be usable on Appveyor (which has it installed and in PATH), by just using `toolset=clang-win`.

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-10-30 04:18:02 UTC  
> Url: https://github.com/boostorg/build/pull/361#issuecomment-434166565  

The new clang-win.jam worked well. Is there a way to choose a VC++ version as the backend headers and libraries, or is the latest VC++ version found always chosen ? The old clang-win had a <compatibility> property but the new one does not have anything like that which I can see.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-30 14:48:05 UTC  
> Url: https://github.com/boostorg/build/pull/361#issuecomment-434330780  

I'm not sure how `clang-cl` picks the version to use. Will need to check the sources. I think that if you invoke it from a developer command prompt, it uses whatever is set by it, but if not, it scans and picks the most recent MSVC toolset (and Windows SDK - these two are separate.)

---

## Comment 5

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:00 UTC  
> Url: https://github.com/boostorg/build/pull/361#issuecomment-932819730  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
