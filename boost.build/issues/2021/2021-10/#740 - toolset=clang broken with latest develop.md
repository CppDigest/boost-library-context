# #740 - toolset=clang broken with latest develop [Closed]

> Username: pdimov  
> Created at: 2021-10-04 23:01:05 UTC  
> Updated at: 2021-10-05 20:45:37 UTC  
> Closed at: 2021-10-04 23:01:29 UTC  
> Url: https://github.com/boostorg/build/issues/740  

Something from the latest changes to develop seems to have broken `toolset=clang`. I'm now getting  
```  
clang-win.link.dll ..\..\bin.v2\libs\container\build\clang-win-12.0.0\debug\cxxstd-17-iso\threading-multi\boost_container-clangw12-mt-gd-x32-1_78.dll  
LINK : fatal error LNK1104: cannot open file 'msvcrtd.lib'  
```  
and I noticed this on Appveyor today as well, although I thought they had broken something, but apparently not.  
  
https://ci.appveyor.com/project/pdimov/core/builds/41017039/job/ch4t3j5hpna4ory7

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2021-10-04 23:01:28 UTC  
> Url: https://github.com/boostorg/build/issues/740#issuecomment-933918002  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues

---

## Comment 2

> Username: pdimov  
> Created at: 2021-10-05 20:45:36 UTC  
> Url: https://github.com/boostorg/build/issues/740#issuecomment-934806031  

https://github.com/bfgroup/b2/issues/98
