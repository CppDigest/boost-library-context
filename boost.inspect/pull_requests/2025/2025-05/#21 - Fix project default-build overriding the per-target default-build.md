# #21 Fix project default-build overriding the per-target default-build. [Merged]

> Username: grafikrobot  
> Created at: 2025-05-07 01:14:34 UTC  
> Updated at: 2025-05-07 02:58:46 UTC  
> Merged at: 2025-05-07 01:33:22 UTC  
> Closed at: 2025-05-07 01:33:22 UTC  
> Url: https://github.com/boostorg/inspect/pull/21  



---

## Comment 1

> Username: pdimov  
> Created_at: 2025-05-07 01:40:20 UTC  
> Url: https://github.com/boostorg/inspect/pull/21#issuecomment-2856775461  

This fixes `b2 build//dist-bin`:  
```  
...patience...  
...patience...  
...found 3643 targets...  
...updating 1 target...  
common.copy C:\boost-git\develop\dist\bin\inspect.exe  
..\..\bin.v2\tools\inspect\build\msvc-14.3\release\x86_64\link-static\threading-multi\inspect.exe  
        1 file(s) copied.  
  
...updated 1 target...  
```  
and `b2 dist-bin`, using the root target, works too:  
```  
...patience...  
...patience...  
...found 3643 targets...  
...updating 1 target...  
common.copy C:\boost-git\develop\dist\bin\inspect.exe  
..\..\bin.v2\tools\inspect\build\msvc-14.3\release\x86_64\link-static\threading-multi\inspect.exe  
        1 file(s) copied.  
  
...updated 1 target...  
```  
However, `b2 dist` still installs `debug` :-)  
```  
...patience...  
...patience...  
...found 3643 targets...  
...updating 1 target...  
common.copy C:\boost-git\develop\dist\bin\inspect.exe  
..\..\bin.v2\tools\inspect\build\msvc-14.3\debug\x86_64\link-static\threading-multi\inspect.exe  
        1 file(s) copied.  
```  
B2 wonders never cease :-)  
  
The `default-build` parameter of the individual targets doesn't seem very useful. Looks like it's only (somewhat) reliable if put on the `project`s.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2025-05-07 02:58:45 UTC  
> Url: https://github.com/boostorg/inspect/pull/21#issuecomment-2856873233  

Nah, it's the same problem. B2 picks the first default encountered while determining the target build request. And depending on where the default is set you get difference results. Here's the equivalent change tot he top-level as the one from before int he build dir.. https://github.com/boostorg/inspect/pull/22

---
