# #291 Enable detection of python's build flags for include dir [Closed]

> Username: gnaggnoyil  
> Created at: 2018-02-24 22:11:08 UTC  
> Updated at: 2021-10-02 20:59:29 UTC  
> Closed at: 2021-06-11 01:54:34 UTC  
> Url: https://github.com/boostorg/build/pull/291  

Many Linux distributions would put python3's include header into directories like `/usr/include/python3.6m` instead of  paths like `/usr/include/python3.6` where `m` is python's abi build flag. Previously the automatic guess for python's header include directory did not consider such situation, thus the commit. The implementation itself is inspired by Python's distutil module(or more precisely, `distuils.sysconfig.get_python_inc` function).

---

## Review 1 [Commented]

> Username: tkelman  
> Created_at: 2018-03-26 21:35:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/291#pullrequestreview-107082216  

📁 src/tools/python.jam

```diff
 551 |+             # indicates this is neither a Python 2 build, nor a build from
 552 |+             # source distro.
 553 |+             includes ?= $(includes)$(sys.abiflags) ;
```

> Username: tkelman  
> Created_at: 2018-03-26 21:35:55 UTC  
> Url: https://github.com/boostorg/build/pull/291#discussion_r177242591  

I think this should be `=` rather than `?=` otherwise it won't change anything.

> Username: gnaggnoyil  
> Created_at: 2018-03-29 23:30:46 UTC  
> Url: https://github.com/boostorg/build/pull/291#discussion_r178206549  

How do `=` and `?=` differ? I'm not very familiar with boost.build and I thought `?=` would skip assignment if variable is already set and `=` would force an assignment.

> Username: tkelman  
> Created_at: 2018-03-29 23:52:09 UTC  
> Updated_at: 2018-03-29 23:55:46 UTC  
> Url: https://github.com/boostorg/build/pull/291#discussion_r178208997  

I'm not that familiar either but when I was trying this patch locally it didn't solve the problem as-is. The setting of `includes` a few lines above on L547 is unconditional (or it's already been set) so it seems this line with a `?=` is resulting in a no-op.


---

## Comment 2

> Username: tkelman  
> Created_at: 2018-03-26 23:38:57 UTC  
> Url: https://github.com/boostorg/build/pull/291#issuecomment-376346921  

It doesn't seem to immediately be breaking anything, but I'll note that on python 2.7, applying this patch now gives  
```  
warning: No python installation configured and autoconfiguration  
note: failed.  See http://www.boost.org/libs/python/doc/building.html  
note: for configuration instructions or pass --without-python to  
note: suppress this message and silently skip all Boost.Python targets  
```  
likely because `sys.abiflags` doesn't exist there?

---

## Comment 3

> Username: gnaggnoyil  
> Created_at: 2018-03-29 23:40:45 UTC  
> Url: https://github.com/boostorg/build/pull/291#issuecomment-377404716  

@tkelman Yes Python 2 does not have `sys.abiflags` nor uses abiflags to form their include directory. But as `local rule probe` indicates when configuring for Python 2 `sys.abiflags` is just void thus `includes` should be left unchanged. I feel a bit strange that such an error would occur. I can confirm such patch works as intended when building for Python 3, I'll see if this patch really has problems for Python 2.

---

## Comment 4

> Username: tkelman  
> Created_at: 2018-03-29 23:53:57 UTC  
> Url: https://github.com/boostorg/build/pull/291#issuecomment-377406780  

We could possibly consider using `distutils.sysconfig` (or `sysconfig` for 2.7 and later) to get the include paths directly, which is the approach used by `python-config`. The assumption that include paths are directly under `sys.prefix` turns out to not be true when using `venv`.

---

## Comment 5

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:54 UTC  
> Url: https://github.com/boostorg/build/pull/291#issuecomment-850859507  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 6

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:28 UTC  
> Url: https://github.com/boostorg/build/pull/291#issuecomment-932819652  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
