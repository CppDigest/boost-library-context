# #482 - Outdated defect macros for Haiku [Open]

> Username: Flamefire  
> Created at: 2023-08-28 07:25:00 UTC  
> Updated at: 2023-08-28 07:40:48 UTC  
> Url: https://github.com/boostorg/config/issues/482  

A user did a portfile (build definition of some sort) for Haiku and noticed that although GCC 13 is being used Boost.Config unconditionally defines C++11 defect macros: https://github.com/boostorg/config/blob/boost-1.83.0/include/boost/config/platform/haiku.hpp#L19-L22  
  
As [reported](https://github.com/boostorg/locale/issues/191#issuecomment-1694695004) those defects no longer apply and are seemingly long outdated (added in Boost 1.58 and never touched since)  
  
I don't know the motivation for adding them but maybe more specific checks regarding GCC and libstdc++ made them superflous at some point. In any case I doubt C++11 defect macros in a platform file are correct, so I'd assume removing them would be ok

---

## Comment 1

> Username: Begasus  
> Created at: 2023-08-28 07:40:48 UTC  
> Url: https://github.com/boostorg/config/issues/482#issuecomment-1695191759  

@jessicah could you have a look here, in the other issue already mentioned that disabling the function check in haiku.hpp made it work to get a full build. TIA :)
