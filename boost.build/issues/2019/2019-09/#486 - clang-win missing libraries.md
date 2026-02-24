# #486 - clang-win missing libraries [Open]

> Username: diorcety  
> Created at: 2019-09-26 12:40:19 UTC  
> Updated at: 2021-05-29 18:22:36 UTC  
> Url: https://github.com/boostorg/build/issues/486  

There is issue with clang-win toolset. The libraries defined in https://github.com/boostorg/log/blob/develop/build/Jamfile.v2 (psapi and secur32) are not put in rsp file, causing undefined symbol during linking stage when compiling boost log.  
  
https://github.com/boostorg/log/pull/91  
https://github.com/boostorg/log/pull/92  
https://ci.appveyor.com/project/Lastique/log/builds/27685606

---

## Comment 1

> Username: pdimov  
> Created at: 2019-12-18 12:56:36 UTC  
> Url: https://github.com/boostorg/build/issues/486#issuecomment-567020154  

This is a problem in clang-win.jam that has been fixed in https://github.com/boostorg/build/commit/2c00d909f4f6c6faf7dce3d7359376e5fe753d67 on develop, but the fix has not yet been merged to master.  
  
There's an additional issue with clang-win 32 bit on Appveyor; linking fails with a "can't find mspdbcore.dll" error. This used to work, so it must be an issue with Appveyor's installation. I only use clang-win 64 bit on Appveyor now as a workaround.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:06 UTC  
> Url: https://github.com/boostorg/build/issues/486#issuecomment-850877334  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
