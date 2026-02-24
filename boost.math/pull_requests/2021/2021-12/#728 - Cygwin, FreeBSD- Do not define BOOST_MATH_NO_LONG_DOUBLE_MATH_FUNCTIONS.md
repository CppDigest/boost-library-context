# #728 Cygwin, FreeBSD: Do not define BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS [Closed]

> Username: mkoeppe  
> Created at: 2021-12-28 06:34:20 UTC  
> Updated at: 2022-01-03 08:48:17 UTC  
> Closed at: 2022-01-03 08:48:17 UTC  
> Url: https://github.com/boostorg/math/pull/728  

Conditionals for these platforms in `include/boost/math/tools/config.hpp` appear to be outdated.  
  
FreeBSD patches it out in its port of SciPy (which vendors boost headers) - https://cgit.freebsd.org/ports/tree/science/py-scipy/files/patch-scipy___lib_boost_boost_math_tools_config.hpp  
  
In SageMath, we have adopted this patch and made the same update to Cygwin - https://trac.sagemath.org/ticket/33080  
  
The Cygwin platform can be tested via #727.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-01-02 11:31:23 UTC  
> Url: https://github.com/boostorg/math/pull/728#issuecomment-1003701422  

@NAThompson and @jzmaddock I think this is a reasonable change if one of you could approve the CI run. Some of the assumptions made in config are very old and worth relooking.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2022-01-02 12:01:08 UTC  
> Url: https://github.com/boostorg/math/pull/728#issuecomment-1003704642  

CI run approved. Change looks reasonable.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-01-02 14:07:16 UTC  
> Url: https://github.com/boostorg/math/pull/728#issuecomment-1003721682  

What I suggest we do is merge this and the Cygwin CI PR into a new branch/PR and then fix up any issues so we have clean CI again before merging the whole lot to develop.

---

## Comment 4

> Username: mborland  
> Created_at: 2022-01-03 08:48:17 UTC  
> Url: https://github.com/boostorg/math/pull/728#issuecomment-1003943657  

Merged into #729 for testing with Cygwin in the CI system.

---
