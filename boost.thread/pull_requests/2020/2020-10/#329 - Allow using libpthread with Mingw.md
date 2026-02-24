# #329 Allow using libpthread with Mingw [Closed]

> Username: petrutlucian94  
> Created at: 2020-10-29 09:26:35 UTC  
> Updated at: 2024-08-05 06:18:41 UTC  
> Closed at: 2024-08-05 06:18:40 UTC  
> Url: https://github.com/boostorg/thread/pull/329  

This pull request provides a few changes required for using libpthread with Mingw:  
* updates the library name  
* allows retrieving the page size on Windows  
  
Those changes are currently cherry-picked when building Ceph for Windows:  
https://github.com/ceph/ceph/blob/master/win32_deps_build.sh#L128-L246

---

## Comment 1

> Username: petrutlucian94  
> Created_at: 2021-01-05 14:08:33 UTC  
> Updated_at: 2021-01-05 14:10:13 UTC  
> Url: https://github.com/boostorg/thread/pull/329#issuecomment-754657748  

The CI failures seem unrelated. Appveyor timed out and Travis failed because of the log size: "The job exceeded the maximum log length, and has been terminated.".

---

## Comment 2

> Username: petrutlucian94  
> Created_at: 2024-08-05 06:18:38 UTC  
> Url: https://github.com/boostorg/thread/pull/329#issuecomment-2268261040  

The project seems abandoned, no activity in almost 4 years. I'm closing this.

---
