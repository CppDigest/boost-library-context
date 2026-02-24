# #702 Fix swapped parameters to `is_substitute` [Merged]

> Username: Kojoley  
> Created at: 2021-10-18 19:47:22 UTC  
> Updated at: 2021-10-20 12:49:16 UTC  
> Merged at: 2021-10-20 12:49:13 UTC  
> Closed at: 2021-10-20 12:49:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/702  

It seems that confusion in template parameters naming lead to mistakenly swapped types to `is_substitute`. The code to X3 have been brought from Qi with the same mistake, but I do not know how to trigger it in Qi.  
  
Fixes #701  
Fixes #679

---

## Comment 1

> Username: Kojoley  
> Created_at: 2021-10-18 19:52:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/702#issuecomment-946112835  

Ahh, I forgot that I saw this code in Qi, here it is https://github.com/boostorg/spirit/blob/ac63f8e6b5b258177ada155678547f3c363ee72d/include/boost/spirit/home/qi/detail/alternative_function.hpp#L38 so Qi should have the same issue. The actual commit that introduces that line is https://github.com/boostorg/spirit/commit/97baf5bbe2f7c13996a6b95c8814ac458bfec419

---

## Comment 2

> Username: Kojoley  
> Created_at: 2021-10-18 19:57:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/702#issuecomment-946116116  

Sadly the commit https://github.com/boostorg/spirit/commit/97baf5bbe2f7c13996a6b95c8814ac458bfec419 did not put a test, and the link http://boost.2283326.n4.nabble.com/Grammar-stopped-compiling-on-more-recent-spirit-version-td3432652.html is dead

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-10-18 20:29:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/702#issuecomment-946140472  

Found that post https://sourceforge.net/p/spirit/mailman/message/27322799/, we have the test https://github.com/boostorg/spirit/blob/4ea7e3a7b7abbf60187194a5a3f790e0f6af6579/test/qi/alternative.cpp#L63-L81 https://github.com/boostorg/spirit/blob/4ea7e3a7b7abbf60187194a5a3f790e0f6af6579/test/qi/alternative.cpp#L259-L266 so it did not regress with swapping, that's good.

---
