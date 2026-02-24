# #616 Remove vc71 fix. [Merged]

> Username: NAThompson  
> Created at: 2021-04-26 18:25:12 UTC  
> Updated at: 2021-04-26 20:11:10 UTC  
> Merged at: 2021-04-26 20:11:06 UTC  
> Closed at: 2021-04-26 20:11:06 UTC  
> Url: https://github.com/boostorg/math/pull/616  

This is a "discussion commit", because I don't know what this was for. It might be useful, or was useful at one time.  
  
But now that boost.math will be distributed alone, I want the directory structure a little cleaner so new users don't get distracted.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-04-26 18:46:42 UTC  
> Updated_at: 2021-04-26 18:47:10 UTC  
> Url: https://github.com/boostorg/math/pull/616#issuecomment-827064913  

There is a [note about VC7.1](https://beta.boost.org/doc/libs/1_33_1/tools/build/v1/vc-7_1-tools.html) on an ancient version of the boost build docs. A compiler that old is in no danger of compiling the current library.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-04-26 18:57:34 UTC  
> Url: https://github.com/boostorg/math/pull/616#issuecomment-827071233  

Nod.  This can all go now since vc7.1 is no longer supported.

---
