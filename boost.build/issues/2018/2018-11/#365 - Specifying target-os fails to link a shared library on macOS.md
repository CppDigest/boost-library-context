# #365 - [cross-build] Specifying target-os fails to link a shared library on macOS [Open]

> Username: theodelrieu  
> Created at: 2018-11-09 11:00:45 UTC  
> Updated at: 2021-06-26 03:09:49 UTC  
> Url: https://github.com/boostorg/build/issues/365  

Hello,  
  
I've stumbled across a weird behaviour related to `target-os` and shared libraries, while building `atomic` (I'm on macOS):  
  
* `cd boost/libs/atomic/build`  
* run `b2 toolset=clang link=shared target-os=iphone`  
  
The macOS linker complains about `--start-group` (`--end-group` is unrecognized as well).  
  
Removing the `target-os` works.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:11 UTC  
> Url: https://github.com/boostorg/build/issues/365#issuecomment-868936372  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
