# #253 - Add detected version feature. [Open]

> Username: grafikrobot  
> Created at: 2017-10-16 21:34:59 UTC  
> Updated at: 2017-12-17 21:25:28 UTC  
> Url: https://github.com/boostorg/build/issues/253  

It's common to want to know, and use, the "real" version of the toolset being used instead of the user configured version tag. Hence we should add a "detected-version" feature to capture and propagate the version that we can detect during configuration of toolsets.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-17 21:25:28 UTC  
> Url: https://github.com/boostorg/build/issues/253#issuecomment-352286767  

It would probably be better to change version to mean this, and rename the user-provided tag to id or something.  It's less confusing and most code that checks the version tends to assume that the version is the "real" version anyway.
