# #8 - Exclusion of static libs for static runtime usage [Open]

> Username: mellery451  
> Created at: 2019-05-22 20:35:56 UTC  
> Updated at: 2019-06-07 11:09:16 UTC  
> Url: https://github.com/boostorg/boost_install/issues/8  

If I do a build with default settings (e.g. just `./b2 install`) on linux, I get dynamic and static libs built using the `system` naming convention. By default, boost assumes a shared runtime. For static libs, however, does it really matter? For instance, my consuming application specifies both `Boost_USE_STATIC_LIBS` and `Boost_USE_STATIC_RUNTIME` and thus the default install fails to find the library because of this cmake config code (just using chrono as an example here) :  
  
```cmake  
# runtime-link=shared  
  
if(Boost_USE_STATIC_RUNTIME)  
    _BOOST_SKIPPED("libboost_chrono.a" "shared runtime, Boost_USE_STATIC_RUNTIME=${Boost_USE_STATIC_RUNTIME}")  
    return()  
endif()  
```  
  
When I switch to a `tagged` layout and build versions for both static and shared runtime, I get two copies of what appear to be the same static library. Couldn't we just make an exception and allow the system layout to work for either runtime choice? I realize this might be OS dependent since windows is clearly different in this regard and apple-clang doesn't allow static runtime at all (I believe).

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-22 20:43:44 UTC  
> Url: https://github.com/boostorg/boost_install/issues/8#issuecomment-494964999  

We could hack around this in principle but it seems to me that setting `Boost_USE_STATIC_RUNTIME` is correct to reject libraries built with the shared runtime. Is the mistake not in the consuming app, which should only request static runtime when it means it, guarded by `if(WIN32)`?

---

## Comment 2

> Username: mellery451  
> Created at: 2019-05-22 20:46:03 UTC  
> Url: https://github.com/boostorg/boost_install/issues/8#issuecomment-494965913  

ok - that's an interesting point. I could see the argument for pushing this on to the consuming side I guess.

---

## Comment 3

> Username: Flamefire  
> Created at: 2019-06-07 11:09:16 UTC  
> Url: https://github.com/boostorg/boost_install/issues/8#issuecomment-499846294  

I agree with @pdimov after checking e.g. FindBoost.  
  
Examples:  
- https://gitlab.kitware.com/cmake/cmake/blob/v3.8.0/Modules/FindBoost.cmake#L183  
```  
# Example to find Boost headers and some *static* libraries::  
#  
#   set(Boost_USE_STATIC_LIBS        ON) # only find static libs  
#   set(Boost_USE_MULTITHREADED      ON)  
#   set(Boost_USE_STATIC_RUNTIME    OFF)  
```  
- https://gitlab.kitware.com/cmake/cmake/blob/v3.8.0/Modules/FindBoost.cmake#L1315 (hard coded suffix "s")  
- https://gitlab.kitware.com/cmake/cmake/blob/v3.8.0/Modules/FindBoost.cmake#L1433  
> To disable the ambiguous behavior, the user need only  
> set Boost_USE_STATIC_RUNTIME either ON or OFF.  
  
Only real difference is on non-versioned layout in which you had shared and static libs of "unknown" properties and FindBoost would pick them anyway.  
  
Question though: Where does static_runtime actually has an effect? Could it be disabled for cases where it *really* has no effect?
