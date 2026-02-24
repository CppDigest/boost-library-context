# #336 - The modular work made libboost_system.so mandatory [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2025-06-26 08:56:55 UTC  
> Updated at: 2025-06-26 22:11:29 UTC  
> Closed at: 2025-06-26 21:55:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/336  

Hi,  
  
With the modular work made in #318 it seems that now we unconditionally do that in `build/Jamfile.v2`:  
  
```  
project  
    : common-requirements <library>$(boost_dependencies)  
```  
  
which I guess (I am not sure) implies that all dependent boost libraries which actually define a .so file are now linked against at link time. At least in my case, when building Boost 1.88, I do end up with libboost_filesystem.so depend on libboost_system.so, while Boost.System was moved to being a header-only library + a compatibility .so file a while back. In #98 you did remove the Boost.System link dependency, but I guess this is now back again.  
  
I have no idea how to fix that.  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2025-06-26 09:12:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/336#issuecomment-3007774308  

Note: maybe a "simple" fix is to ask the Boost.System folks to stop shipping a fake libboost_system.so now ? It's been years now that this library is header only, probably the .so file compatibility is no longer necessary now.

---

## Comment 2

> Username: Lastique  
> Created at: 2025-06-26 21:55:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/336#issuecomment-3010263420  

Thanks for the report.  
  
Since Peter decided to remove the compiled Boost.System library, I'm going to close this as fixed upstream. If that was not the case, the solution would be for Boost.System to provide a separate target for header-only configuration and switch to that dependency in Boost.Filesystem.  
  
Also, since this is a fallout from Boost.Build modularization effort I'm going to FYI @grafikrobot. There may be other cases similar to this, which I think should be addressed by Boost.Build team as this is a regression from the prior state.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2025-06-26 22:11:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/336#issuecomment-3010301949  

System was switched to header only a short time ago.. https://github.com/boostorg/system/commit/7a495bb46d7ccd808e4be2a6589260839b0fd3a3
