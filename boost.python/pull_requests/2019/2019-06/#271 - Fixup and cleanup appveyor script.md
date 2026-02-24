# #271 Fixup and cleanup appveyor script [Closed]

> Username: cas--  
> Created at: 2019-06-27 10:16:44 UTC  
> Updated at: 2021-06-07 17:24:56 UTC  
> Closed at: 2021-06-07 17:24:56 UTC  
> Url: https://github.com/boostorg/python/pull/271  

The VS2017 build had specified an MSVC version that was likely to  
change so use the default `14.0` instead.  
  
- Bump the VS2017 build to use Python 3.7.  
- Remove obsolete Python install script.  
- Remove outdated MSVC Express workaound since default is now VS2015.  
- Use pip to install faber and sphinx.

---
