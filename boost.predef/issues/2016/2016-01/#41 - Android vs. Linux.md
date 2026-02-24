# #41 - Android vs. Linux [Closed]

> Username: GBuella  
> Created at: 2016-01-10 17:18:23 UTC  
> Updated at: 2019-01-10 19:27:51 UTC  
> Closed at: 2019-01-10 19:27:51 UTC  
> Url: https://github.com/boostorg/predef/issues/41  

Currently one of BOOST_OS_LINUX and BOOST_OS_ANDROID get to be defined. This is pretty misleading, and results in a lot of  
# if BOOST_OS_LINUX || BOOST_OS_ANDROID  
  
kind of boilerplate.  
Could this be fixed by defining BOOST_OS_LINUX as well, when Android is detected? Or adding a macro BOOST_KERNEL_LINUX?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2017-06-04 15:01:01 UTC  
> Url: https://github.com/boostorg/predef/issues/41#issuecomment-306045249  

Part of the Predef API is to only define one of any symbol to try and avoid the opposite case you are running into. The solution for the OS defs is usually to define a platform define instead. In this case it would be that we need to move Android from being an OS to to being a platform. I.e. defining `BOOST_PLAT_ANDROID` instead of `BOOST_OS_ANDROID`. Does that seem like a reasonable change for you use?

---

## Comment 2

> Username: jeking3  
> Created at: 2017-11-10 22:57:31 UTC  
> Url: https://github.com/boostorg/predef/issues/41#issuecomment-343608945  

PLAT seems reasonable to me, much like the CloudABI platform running on whatever kernel.
