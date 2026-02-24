# #1131 - VS 2026 Bootstrap fail [Closed]

> Username: larsyxa  
> Created at: 2025-12-16 11:50:32 UTC  
> Updated at: 2025-12-16 12:37:31 UTC  
> Closed at: 2025-12-16 12:37:31 UTC  
> Url: https://github.com/boostorg/json/issues/1131  

Bootstrap (1.190.0) with VS 2026 does not work.  
From VC++ dev commandprompt.  
  
C:\CC_BUILD\boosting>bootstrap  
Building Boost.Build engine  
LOCALAPPDATA=C:\Users\************\AppData\Local  
Found with vswhere C:\Program Files\Microsoft Visual Studio\18\Enterprise  
###  
### "Unknown toolset: vcunk"  
###  
### You can specify the toolset as the argument, i.e.:  
###     .\build.bat msvc  
###  
### Toolsets supported by this script are: borland, como, gcc,  
###     clang, clang-win, gcc-nocygwin, intel-win32, mingw,  
###     vc12, vc14, vc141, vc142, vc143  
###  
### If you have Visual Studio 2017 installed you will need to either update  
### the Visual Studio 2017 installer or run from VS 2017 Command Prompt  
### as we where unable to detect your toolset installation.  
###  
  
Failed to build Boost.Build engine.

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-12-16 12:37:23 UTC  
> Url: https://github.com/boostorg/json/issues/1131#issuecomment-3660328416  

B2's bootstrap is in the purview of bfgroup/b2, not this project. I would have directed you there, but seeing [this comment](https://github.com/bfgroup/b2/issues/473#issuecomment-3657873217), you've already reported it there. As was answered in that thread, Boost 1.90.0 _source distribution_ will never have support for this compiler. When a new version of b2 is released, you can use it with Boost 1.90.0. Regardless, this is unrelated to Boost.JSON.
