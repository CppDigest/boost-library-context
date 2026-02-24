# #36 Resolve symlinks in _BOOST_CMAKEDIR [Merged]

> Username: AlD  
> Created at: 2020-03-22 11:58:53 UTC  
> Updated at: 2020-03-22 17:58:16 UTC  
> Merged at: 2020-03-22 16:00:30 UTC  
> Closed at: 2020-03-22 16:00:30 UTC  
> Url: https://github.com/boostorg/boost_install/pull/36  

Fixes the issue of setting Boost_INCLUDE_DIR to "/include" on systems where  
/lib or /lib64 are symlinks to /usr/lib.

---
