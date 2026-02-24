# #37 - Document that BOOST_ATOMIC_NO_CMPXCHG8B may be not useful for MSVC [Closed]

> Username: AlexGuteniev  
> Created at: 2020-06-12 09:09:53 UTC  
> Updated at: 2020-06-12 09:49:18 UTC  
> Closed at: 2020-06-12 09:21:56 UTC  
> Url: https://github.com/boostorg/atomic/issues/37  

Windows requires:  
 * `cmpxchg8b` starting Windows XP  
 * `cmpxchg16b` starting Windows 8  
  
Visual Studio 2012 thru 2019 support Windows XP as the minimum version, and it even takes special toolset to be used.  (Actually even 2019 supports XP by using the toolset from VS 20117).  
  
It means that it is not useful to use `BOOST_ATOMIC_NO_CMPXCHG8B` starting VS 2012.  
Consider documenting it.

---

## Comment 1

> Username: AlexGuteniev  
> Created at: 2020-06-12 09:21:56 UTC  
> Url: https://github.com/boostorg/atomic/issues/37#issuecomment-643171474  

Invalid issue, I misread that   
  
> Affects 32-bit x86 Oracle Studio builds

---

## Comment 2

> Username: Lastique  
> Created at: 2020-06-12 09:49:18 UTC  
> Url: https://github.com/boostorg/atomic/issues/37#issuecomment-643183332  

Whether a particular toolset supports platforms without `cmpxchg8b` or not is not relevant. This macro controls Boost.Atomic. Whether it is useful or not is user's decision.
