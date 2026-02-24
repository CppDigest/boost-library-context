# #149 Fix/msvc12.0 devector issues [Merged]

> Username: viboes  
> Created at: 2017-09-17 22:16:59 UTC  
> Updated at: 2018-02-16 06:12:32 UTC  
> Merged at: 2017-09-18 22:14:36 UTC  
> Closed at: 2017-09-18 22:14:36 UTC  
> Url: https://github.com/boostorg/thread/pull/149  



---

## Comment 1

> Username: pdimov  
> Created_at: 2017-09-18 23:56:06 UTC  
> Url: https://github.com/boostorg/thread/pull/149#issuecomment-330388701  

It would be better to use `BOOST_MSVC` instead of `_MSC_VER` (as the latter is set by other compilers as well.)  
  
In addition, `#if !BOOST_WORKAROUND(BOOST_MSVC, <= 1900)` might be a better check. (1900 is msvc-14.0, Visual Studio 2015.)

---

## Comment 2

> Username: viboes  
> Created_at: 2017-09-18 23:59:52 UTC  
> Url: https://github.com/boostorg/thread/pull/149#issuecomment-330389210  

Thanks Peter. I will fix it.

---
