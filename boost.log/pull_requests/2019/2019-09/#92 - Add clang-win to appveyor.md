# #92 Add clang-win to appveyor [Closed]

> Username: diorcety  
> Created at: 2019-09-26 11:14:59 UTC  
> Updated at: 2019-12-19 15:01:35 UTC  
> Closed at: 2019-09-26 12:33:21 UTC  
> Url: https://github.com/boostorg/log/pull/92  



---

## Comment 1

> Username: Lastique  
> Created_at: 2019-09-26 12:33:21 UTC  
> Url: https://github.com/boostorg/log/pull/92#issuecomment-535481272  

I can see the build is failing but I can't see the command line arguments as they are hidden in the .rsp file. As I suggested in https://github.com/boostorg/log/pull/91#issuecomment-535414676, please investigate the cause on your end. Are the missing libraries specified in the .rsp file? If yes then please report the bug to the clang-win developers. If not or the command line options are incorrect then report the problem to [Boost.Build](https://github.com/boostorg/build/issues/new).  
  
As the PR is failing and I'm not planning to add workarounds for this compiler in Boost.Log, I'm going to close the PR, sorry.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-18 13:02:10 UTC  
> Url: https://github.com/boostorg/log/pull/92#issuecomment-567022039  

The issue in clang-win.jam has been fixed in https://github.com/boostorg/build/commit/2c00d909f4f6c6faf7dce3d7359376e5fe753d67 on develop, but the fix is not on master yet.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-12-19 07:52:19 UTC  
> Url: https://github.com/boostorg/log/pull/92#issuecomment-567377385  

Yeah, well, I just recently had to disable it for Boost.Filesystem because of missing some mspdb.dll error or something like that. I'm done with clang-win for now.

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-12-19 15:01:35 UTC  
> Url: https://github.com/boostorg/log/pull/92#issuecomment-567524526  

The missing mspdbcore.dll error is only on address-model=32, so I restricted my clang-win Appveyor jobs to 64 bit instead of removing them entirely.

---
