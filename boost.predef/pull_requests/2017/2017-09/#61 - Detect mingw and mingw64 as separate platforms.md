# #61 Detect mingw and mingw64 as separate platforms [Merged]

> Username: jeking3  
> Created at: 2017-09-29 20:09:43 UTC  
> Updated at: 2017-10-16 23:41:34 UTC  
> Merged at: 2017-10-16 23:41:34 UTC  
> Closed at: 2017-10-16 23:41:34 UTC  
> Url: https://github.com/boostorg/predef/pull/61  

They are different distributions with different capabilities and should be detected separately.  
  
The definition of ``BOOST_PLAT_MINGW`` is still the same as before: available if the compile is under MinGW or MinGW-w64.  
  
Two new definitions were added, ``BOOST_PLAT_MINGW32`` and ``BOOST_PLAT_MINGW64`` which specifically detect either respectively.  This maintains backwards compatibility, and I didn't deprecate anything.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-04 13:53:52 UTC  
> Url: https://github.com/boostorg/predef/pull/61#issuecomment-334162593  

@grafikrobot this is ready for review.  Passed CI.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-04 20:07:29 UTC  
> Url: https://github.com/boostorg/predef/pull/61#issuecomment-334273942  

@grafikrobot looks like you added travis CI integration; should kick a rebuild on this one too.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-16 13:06:30 UTC  
> Url: https://github.com/boostorg/predef/pull/61#issuecomment-336880740  

@grafikrobot I restructured this PR so that it is backwards compatible.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2017-10-16 17:31:08 UTC  
> Url: https://github.com/boostorg/predef/pull/61#issuecomment-336961630  

Thanks for restructuring.. Makes it much easier to accept when it's back compatible. And it means that users can make choices that cover both easier. One item to is that even though the new files are copy-paste+edit, you should really put a copyright for yourself on them.

---
