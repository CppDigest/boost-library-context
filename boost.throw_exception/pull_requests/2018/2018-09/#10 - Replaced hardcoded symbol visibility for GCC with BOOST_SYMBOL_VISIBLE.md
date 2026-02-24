# #10 Replaced hardcoded symbol visibility for GCC with BOOST_SYMBOL_VISIBLE [Merged]

> Username: epvbergen  
> Created at: 2018-09-25 08:42:32 UTC  
> Updated at: 2018-09-27 03:01:39 UTC  
> Merged at: 2018-09-27 03:01:38 UTC  
> Closed at: 2018-09-27 03:01:38 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/10  

Possible fix for https://github.com/boostorg/throw_exception/issues/9. Any feedback? What tests would you want to see to validate this?

---

## Comment 1

> Username: epvbergen  
> Created_at: 2018-09-25 08:51:12 UTC  
> Updated_at: 2018-09-25 08:51:51 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/10#issuecomment-424259131  

Note: using BOOST_SYMBOL_VISIBLE probably depends on PR #6 .

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-09-25 17:58:20 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/10#issuecomment-424441683  

Looks good but the convention throughout Boost seems to be `class BOOST_SYMBOL_VISIBLE X`, not `BOOST_SYMBOL_VISIBLE class X`.  
  
It probably makes no difference but it doesn't hurt to be consistent.

---

## Comment 3

> Username: pgroke-dt  
> Created_at: 2018-09-25 18:03:09 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/10#issuecomment-424443436  

If `BOOST_SYMBOL_VISIBLE` can expand to `__declspec(dllexport)` then `class BOOST_SYMBOL_VISIBLE X` is the only placement that will work.

---
