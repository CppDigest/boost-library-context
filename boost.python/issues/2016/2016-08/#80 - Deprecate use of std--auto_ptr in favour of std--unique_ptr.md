# #80 - Deprecate use of std::auto_ptr in favour of std::unique_ptr [Closed]

> Username: stefanseefeld  
> Created at: 2016-08-05 14:05:56 UTC  
> Updated at: 2017-12-31 19:17:23 UTC  
> Closed at: 2016-09-27 18:36:19 UTC  
> Url: https://github.com/boostorg/python/issues/80  

Replace all **internal** usages of `std::auto_ptr` by `std::unique_ptr` whenever the compiler supports it. Support for `std::auto_ptr` in user code needs to be maintained for backward-compatibility.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-09-27 18:35:35 UTC  
> Url: https://github.com/boostorg/python/issues/80#issuecomment-249956442  

This is addressed in https://github.com/boostorg/python/commit/5029273ca8e6c18bfc3894de0cc3011f091988f6.

---

## Comment 2

> Username: meshula  
> Created at: 2017-12-31 19:13:08 UTC  
> Updated at: 2017-12-31 19:17:23 UTC  
> Url: https://github.com/boostorg/python/issues/80#issuecomment-354619692  

For others investigating build failures in VS2017, this issue was re-addressed by guarding auto_ptr with BOOST_NO_CXX11_SMART_PTR. The fix is not released in 1.65.1...
