# #89 - Complete Boost.Build infrastructure for NumPy integration. [Closed]

> Username: stefanseefeld  
> Created at: 2016-10-08 20:12:05 UTC  
> Updated at: 2016-10-29 04:50:16 UTC  
> Closed at: 2016-10-29 04:50:16 UTC  
> Url: https://github.com/boostorg/python/issues/89  

I just merged a NumPy extension from https://github.com/ndarray/Boost.NumPy, such that it is built and tested using the SCons-based build system.  
This still lacks integration with the Boost.Build infrastructure, such that it can be built and tested as part of the rest of Boost, too.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-10-14 18:41:43 UTC  
> Url: https://github.com/boostorg/python/issues/89#issuecomment-253886935  

A partial implementation is here:  
https://github.com/stefanseefeld/boost.python/commit/586932874b09e25511b64f85c2ebfa6a76edc3dd  
This works fine on my Linux laptop, but needs some cleanup...

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-10-29 04:50:16 UTC  
> Url: https://github.com/boostorg/python/issues/89#issuecomment-257070925  

A partial implementation is here:  
https://github.com/stefanseefeld/boost.python/commit/586932874b09e25511b64f85c2ebfa6a76edc3dd  
This works fine on my Linux laptop, but needs some cleanup...
