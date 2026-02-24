# #150 - Appveyor appears to be broken. [Closed]

> Username: garyfurnish  
> Created at: 2017-08-21 20:47:20 UTC  
> Updated at: 2017-11-14 15:21:32 UTC  
> Closed at: 2017-11-14 15:21:32 UTC  
> Url: https://github.com/boostorg/python/issues/150  

It appears that the appveyor tests always fail.  
It might be that even selecting a 2017 environment, scons is finding the wrong compiler so it is not testing with 2017 but is always testing with 2015. An upgrade to scons may be necessary.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-11-14 15:21:32 UTC  
> Url: https://github.com/boostorg/python/issues/150#issuecomment-344292330  

I have switched from `SCons` to `Faber` for the CI build system (and will likely retire the SCons-based build system in the near future). The new infrastructure works much better. It notably doesn't fail just because individual tests fail. Now we can concentrate on Boost.Python test failures (which for the most part seem MSVC compiler limitations and bugs to work around).
