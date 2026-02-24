# #188 - vcvarsall.bat is not recognized as an internal or external command [Open]

> Username: OlafvdSpek  
> Created at: 2017-03-30 17:34:30 UTC  
> Updated at: 2017-06-06 02:16:35 UTC  
> Url: https://github.com/boostorg/build/issues/188  

When building 1.64 beta 2 on VC2017 / W7, this is logged over and over but the build still seems to finish OK.

---

## Comment 1

> Username: nooperation  
> Created at: 2017-06-06 02:16:35 UTC  
> Url: https://github.com/boostorg/build/issues/188#issuecomment-306362271  

You possibly had the incorrect toolset version set for VC2017, which would cause these errors.  
  
The correct toolset for the current build of VS2017 is **msvc-14.1**
