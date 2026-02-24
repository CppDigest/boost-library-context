# #768 ci: move GitHub Action's Windows builds to supported OS version [Merged]

> Username: striezel  
> Created at: 2025-07-31 16:21:53 UTC  
> Updated at: 2025-08-03 17:30:35 UTC  
> Merged at: 2025-08-03 09:07:17 UTC  
> Closed at: 2025-08-03 09:07:17 UTC  
> Url: https://github.com/boostorg/gil/pull/768  

### Description  
  
The Windows Server 2019 runner image has been removed as of 2025-06-30, so it's not available anymore. For further information on that see <https://github.com/actions/runner-images/issues/12045>.  
  
However, we can test on the newer Windows Server 2022 image instead.  
  
Unfortunately, the Windows Server 2019 image was the last one to have Visual Studio 2019, so from now on only Visual Studio 2022 can be used in the CI workflows for MSVC.  
  
We _could_ also use the Windows Server 2025 image, but it has Visual Studio 2022, too, just like the Windows Server 2022 image. So this would probably not make any difference.  
  
### References  
  
https://github.com/actions/runner-images/issues/12045  
  
### Tasklist  
  
- [x] Ensure all CI builds for Windows OS pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2025-08-03 09:06:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/768#pullrequestreview-3082032058  

@striezel Thank you very much!  
  
I think it's best to simply use the current environments available on GitHub, without trying to set up custom variants, as it is always maintenance pain. A trade-off, but I see no better way, given limited resources for the maintenance of the project.

---
