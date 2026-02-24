# #88 Add version information to os/cygwin detection [Merged]

> Username: jeking3  
> Created at: 2018-11-01 20:10:06 UTC  
> Updated at: 2018-11-06 14:56:12 UTC  
> Merged at: 2018-11-06 14:56:12 UTC  
> Closed at: 2018-11-06 14:56:12 UTC  
> Url: https://github.com/boostorg/predef/pull/88  



---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2018-11-01 20:18:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/predef/pull/88#pullrequestreview-170864443  

📁 include/boost/predef/os/cygwin.h

```diff
  32 |- #   define BOOST_OS_CYGWIN BOOST_VERSION_NUMBER_AVAILABLE
  35 |+ #   define BOOST_OS_CYGWIN \
  36 |+         BOOST_VERSION_NUMBER(CYGWIN_VERSION_API_MAJOR,\
```

> Username: grafikrobot  
> Created_at: 2018-11-01 20:18:16 UTC  
> Updated_at: 2018-11-01 20:18:35 UTC  
> Url: https://github.com/boostorg/predef/pull/88#discussion_r230183073  

Are the `CYGWIN_VERSION_API_*` macros defined in older versions of cygwin? If not we need to guard against them not being defined and defaulting to `BOOST_VERSION_NUMBER_AVAILABLE`.

> Username: jeking3  
> Created_at: 2018-11-01 20:21:09 UTC  
> Updated_at: 2018-11-01 20:21:10 UTC  
> Url: https://github.com/boostorg/predef/pull/88#discussion_r230184083  

All the way back to at least 2000.   
  
https://github.com/Alexpux/Cygwin/commit/4b17897e984a3f743e801b9321ee4d3e39912df3#diff-447ff859802166057451911061326417

> Username: grafikrobot  
> Created_at: 2018-11-01 20:24:16 UTC  
> Url: https://github.com/boostorg/predef/pull/88#discussion_r230185105  

In that case it looks fine :-)


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2018-11-01 20:24:42 UTC  
> Url: https://github.com/boostorg/predef/pull/88#issuecomment-435174707  

PS. I await the CI tests for merge.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-11-01 20:44:00 UTC  
> Url: https://github.com/boostorg/predef/pull/88#issuecomment-435180603  

That's reasonable, though the CI tests won't actually test this other than to parse the file.  See https://github.com/boostorg/predef/issues/87

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-11-06 13:58:22 UTC  
> Url: https://github.com/boostorg/predef/pull/88#issuecomment-436261466  

Reminder to merge for 1.69.0.

---
