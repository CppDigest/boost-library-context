# #20 Add backward compat dist-bin target. [Merged]

> Username: grafikrobot  
> Created at: 2025-05-06 20:40:53 UTC  
> Updated at: 2025-05-07 01:15:55 UTC  
> Merged at: 2025-05-07 00:11:22 UTC  
> Closed at: 2025-05-07 00:11:22 UTC  
> Url: https://github.com/boostorg/inspect/pull/20  



---

## Comment 1

> Username: pdimov  
> Created_at: 2025-05-06 23:51:55 UTC  
> Url: https://github.com/boostorg/inspect/pull/20#issuecomment-2856588163  

Can't `build//dist-bin` be just an `alias` for the root `dist-bin` target?

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2025-05-06 23:56:27 UTC  
> Url: https://github.com/boostorg/inspect/pull/20#issuecomment-2856592755  

> Can't `build//dist-bin` be just an `alias` for the root `dist-bin` target?  
  
I'd have to try that.. But I suspect it might cause recursive problems.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-05-07 00:13:26 UTC  
> Url: https://github.com/boostorg/inspect/pull/20#issuecomment-2856631727  

Seems to work for me. But we have the same problem here as we did with Quickbook:  
  
```  
msvc.link ..\..\bin.v2\tools\inspect\build\msvc-14.3\debug\x86_64\link-static\threading-multi\inspect.exe  
common.copy C:\boost-git\develop\dist\bin\inspect.exe  
..\..\bin.v2\tools\inspect\build\msvc-14.3\debug\x86_64\link-static\threading-multi\inspect.exe  
```  
  
That is, the `debug` variant is installed by default when doing `b2 build//dist-bin`.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2025-05-07 01:15:53 UTC  
> Url: https://github.com/boostorg/inspect/pull/20#issuecomment-2856744792  

@pdimov this https://github.com/boostorg/inspect/pull/21 fixes the debug/release problem. Just had to think about what was going on to eventually realize that it was obvious. :-)

---
