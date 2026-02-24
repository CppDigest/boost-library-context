# #77 Turn off BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS for clang++ [Merged]

> Username: hia3  
> Created at: 2019-01-14 18:35:34 UTC  
> Updated at: 2020-04-02 05:40:35 UTC  
> Merged at: 2019-02-19 19:59:43 UTC  
> Closed at: 2019-02-19 19:59:43 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77  



---

## Comment 1

> Username: jeking3  
> Created_at: 2019-01-14 20:00:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454141495  

We should probably switch to Boost.Predef here and detect dinkumware itself...

---

## Comment 2

> Username: hia3  
> Created_at: 2019-01-14 20:49:13 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454156077  

I have two variants:  
  
1. Similar to [this](https://github.com/boostorg/unordered/blob/develop/include/boost/unordered/detail/fwd.hpp#L32-L34).  
  
    #include <boost/predef.h>  
    ...  
    // I have no idea when Dinkumware added it, probably a lot  
    // earlier than this check.  
    #if defined(BOOST_LIB_STD_DINKUMWARE) && (BOOST_LIB_STD_DINKUMWARE >= BOOST_VERSION_NUMBER(6, 50, 0))  
  
  
2. Or:  
  
    #include <boost/predef.h>  
    ...  
    #if defined(BOOST_LIB_STD_DINKUMWARE) && (_MSVC_STL_VERSION >= 141)  
  
Trying to summon @BillyONeal

---

## Comment 3

> Username: BillyONeal  
> Created_at: 2019-01-14 20:52:01 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454156907  

Hi there, my original PR didn't guard for compiler at all. I think this is a library property and `_MSVC_STL_VERSION` should be sufficient to identify but some maintainers were unhappy with that...

---

## Comment 4

> Username: BillyONeal  
> Created_at: 2019-01-14 20:53:59 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454157533  

> We should probably switch to Boost.Predef here and detect dinkumware itself...  
  
The difficulty here is that MSVC++'s standard library is a Dinkumware derivative but is no longer actually Dinkumware; so using a Dinkumware test to detect us will likely do the wrong thing on other Dinkumware licensees (e.g. I've heard rumors that Playstation still uses Dinkumware?).

---

## Comment 5

> Username: jeking3  
> Created_at: 2019-01-14 21:53:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454175764  

So `_CPPLIB_VER` identifies dinkumware, or a dinkumware derivitive, however we only want to set this `BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS` on MSVC if STL is version 140 or earlier, is that correct?  And the change here is that MSVC started shipping with clang support built-in, so this change accounts for that case which was missing?

---

## Comment 6

> Username: BillyONeal  
> Created_at: 2019-01-14 22:48:06 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454190999  

> BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS on MSVC if STL is version 140 or earlier, is that correct?  
  
Right; other Dinkumware licensees likely still have the bigger-than-2-GiB file handling bugs we fixed.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-01-15 00:25:34 UTC  
> Updated_at: 2019-01-15 00:26:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454217748  

I see no reason to check the compiler macros here. `_MSVC_STL_VERSION >= 141` should be enough.  
  
edit: or rather, `defined(_MSVC_STL_VERSION) && _MSVC_STL_VERSION >= 141` should be enough, to avoid a warning when `_MSVC_STL_VERSION` isn't defined.

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-01-15 00:34:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454219428  

Original PR for reference: https://github.com/boostorg/iostreams/pull/57

---

## Comment 9

> Username: jeking3  
> Created_at: 2019-01-15 00:46:46 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454221709  

That's what I was thinking, but I wanted to clarify.  Thanks @pdimov!

---

## Review 10 [Changes requested]

> Username: jeking3  
> Created_at: 2019-01-15 03:11:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iostreams/pull/77#pullrequestreview-192472120  

Can we simplify this entire expression down to:  
  
#if defined(_CPPLIB_VER) && (!defined(_MSVC_STL_VERSION) || _MSVC_STL_VERSION < 141)  
  
perhaps?

---

## Comment 11

> Username: pdimov  
> Created_at: 2019-01-15 03:41:35 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454256878  

`_YVALS` is probably obsolete; I don't think we support pre-`_CPPLIB_VER` Dinkumware. Checking `__SGI_STL_PORT` or `_STLPORT_VERSION` seems redundant; I don't see how you can use both Dinkumware and STLport at the same time. Although who knows what happens on esoteric platforms. `__QNX__` and `__VXWORKS__` are probably checked because they use Dinkumware too? Not sure.

---

## Comment 12

> Username: pdimov  
> Created_at: 2019-01-15 03:47:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454257842  

https://github.com/boostorg/iostreams/pull/13 and https://github.com/boostorg/iostreams/pull/49 introduce the checks for VxWorks. Presumably QNX uses a similar Dinkumware version.  
  
The STLPort checks come from https://github.com/boostorg/iostreams/commit/2d11830721a190083aeec1b937608738276492ee, without much of a history.

---

## Comment 13

> Username: pdimov  
> Created_at: 2019-01-15 03:53:28 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454258638  

Some more history: https://github.com/boostorg/iostreams/commit/3a18bc413223c80f584249782c01ab3da40da03e, https://github.com/boostorg/iostreams/commit/ccf854861c039a815315ddb8dd6fb3e3f6a5fd88

---

## Comment 14

> Username: codecov[bot]  
> Created_at: 2019-01-15 10:18:16 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454339648  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/77?src=pr&el=h1) Report  
> Merging [#77](https://codecov.io/gh/boostorg/iostreams/pull/77?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/29a831798ec6337848d1f2e8595b59e5503e4f21?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/77/graphs/tree.svg?width=650&token=LBEfwtNfca&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/77?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #77   +/-   ##  
========================================  
  Coverage    64.73%   64.73%             
========================================  
  Files           81       81             
  Lines         3547     3547             
  Branches       950      950             
========================================  
  Hits          2296     2296             
  Misses         461      461             
  Partials       790      790  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/77?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/77?src=pr&el=footer). Last update [29a8317...4d2bbc5](https://codecov.io/gh/boostorg/iostreams/pull/77?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 15

> Username: jeking3  
> Created_at: 2019-01-15 14:27:52 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-454410369  

Sadly we cannot test these platforms in our CI environment, so the safest thing to do is leave what's there and make the smallest modification possible.

---

## Comment 16

> Username: K-M-Ibrahim-Khalilullah  
> Created_at: 2020-04-02 05:40:35 UTC  
> Url: https://github.com/boostorg/iostreams/pull/77#issuecomment-607632507  

@BillyONeal   
I installed boost with pcl. Now how can I turn off for making it usable in my project

---
