# #18 Fixing build break with Visual Studio if phone scripts aren't installed with VS 2012/2013 [Merged]

> Username: stgates  
> Created at: 2014-07-11 08:37:23 UTC  
> Updated at: 2021-10-02 22:35:54 UTC  
> Merged at: 2014-07-11 08:44:28 UTC  
> Closed at: 2014-07-11 08:44:28 UTC  
> Url: https://github.com/boostorg/build/pull/18  

Fixes a late issue discovered that can cause build issues if Visual Studio 2012 or 2013 doesn't actually contain the Windows Phone setup scripts.  
  
This was caused from pull request https://github.com/boostorg/build/pull/14. I've tested now on a machine without the phone setup scripts for Visual Studio 2012 and 2013.

---
