# #83 - Predef uses deprecated macro TARGET_IPHONE_SIMULATOR [Closed]

> Username: ldionne  
> Created at: 2018-08-27 15:47:10 UTC  
> Updated at: 2019-01-13 14:53:30 UTC  
> Closed at: 2019-01-13 14:53:30 UTC  
> Url: https://github.com/boostorg/predef/issues/83  

Since iOS 9, the `TARGET_IPHONE_SIMULATOR` macro has been deprecated and `TARGET_OS_SIMULATOR` was introduced to replace it. The deprecated macro is still used in [platform/ios.h](https://github.com/boostorg/predef/blob/d8f410b4d0c16fb4146b4cda7cbd324d325bf90a/include/boost/predef/platform/ios.h#L32):  
  
```c++  
#if BOOST_OS_IOS  
#    include <TargetConditionals.h>  
#    if TARGET_IPHONE_SIMULATOR == 1  
#        undef BOOST_PLAT_IOS_SIMULATOR  
#        define BOOST_PLAT_IOS_SIMULATOR BOOST_VERSION_NUMBER_AVAILABLE  
#    else  
#        undef BOOST_PLAT_IOS_DEVICE  
#        define BOOST_PLAT_IOS_DEVICE BOOST_VERSION_NUMBER_AVAILABLE  
#    endif  
#endif  
```  
  
It would be nice to use the non-deprecated macro. If Predef cares about supporting pre-iOS 9, it could perhaps use the deprecated macro on iOS < 9 and the new macro otherwise.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-01-11 04:11:54 UTC  
> Url: https://github.com/boostorg/predef/issues/83#issuecomment-453372333  

@ldionne do you happen to have a link to the latest `TargetConditionals.h` or current equivalent header where the new predef is defined?
