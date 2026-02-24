# #58 - BOOST_SYSTEM_USE_UTF8 [Closed]

> Username: aholzinger  
> Created at: 2021-01-09 17:18:06 UTC  
> Updated at: 2021-01-09 17:58:12 UTC  
> Closed at: 2021-01-09 17:58:12 UTC  
> Url: https://github.com/boostorg/system/issues/58  

In this document [https://www.boost.org/doc/libs/1_75_0/libs/system/doc/html/system.html](url) the `#define BOOST_SYSTEM_USE_UTF8` is explained, but it's not explained if it suffices to define `BOOST_SYSTEM_USE_UTF8` when using boost (that would mean that all Boost code that is depending on `BOOST_SYSTEM_USE_UTF8` is header-only) or if `BOOST_SYSTEM_USE_UTF8` has to be defined already when Boost is built.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-01-09 17:28:11 UTC  
> Url: https://github.com/boostorg/system/issues/58#issuecomment-757339340  

It should also be defined when building Boost. You can see a list of libraries using Boost.System [here](https://pdimov.github.io/boostdep-report/boost-1.75.0/system.html#reverse-dependencies) and not all of them are header-only.

---

## Comment 2

> Username: aholzinger  
> Created at: 2021-01-09 17:58:12 UTC  
> Url: https://github.com/boostorg/system/issues/58#issuecomment-757343226  

Thanks
