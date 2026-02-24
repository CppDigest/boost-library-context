# #420 Switch AppVeyor to use VS2017 [Merged]

> Username: xiangfan-ms  
> Created at: 2018-08-30 00:18:02 UTC  
> Updated at: 2018-08-30 14:50:43 UTC  
> Merged at: 2018-08-30 14:50:43 UTC  
> Closed at: 2018-08-30 14:50:43 UTC  
> Url: https://github.com/boostorg/hana/pull/420  

The old config uses VS2015 and never works. This PR switches to use VS2017 instead.  
  
The config currently uses clang-cl which will fail due to some compiler errors.  
Once AppVeyor supports VS2017 15.8, we can switch the compiler to MSVC.

---
