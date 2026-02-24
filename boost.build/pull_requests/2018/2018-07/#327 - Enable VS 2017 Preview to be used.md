# #327 Enable VS 2017 Preview to be used [Closed]

> Username: IvanFarkas  
> Created at: 2018-07-29 20:39:15 UTC  
> Updated at: 2021-10-02 21:18:56 UTC  
> Closed at: 2019-05-06 15:24:12 UTC  
> Url: https://github.com/boostorg/build/pull/327  

# This feature enables VS 2017 Preview to be used.  
  
## Changes  
  
Pass ```bootstrap.bat prerelease``` parameter to ```config_toolset.bat```, ```guess_toolset.bat``` and ```vswhere_usability_wrapper.cmd``` scripts.  
  
**Fixed typos**  
- VSWHERE_WITH_PRERELASE -> **VSWHERE_WITH_PRERELEASE**  
- prerelase -> **prerelease**

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2019-05-06 15:24:12 UTC  
> Url: https://github.com/boostorg/build/pull/327#issuecomment-489661599  

Obsolete, scripts were rewritten.

---
