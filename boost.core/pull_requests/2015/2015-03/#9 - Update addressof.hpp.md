# #9 Update addressof.hpp [Merged]

> Username: akumta  
> Created at: 2015-03-03 19:38:38 UTC  
> Updated at: 2015-03-08 21:37:18 UTC  
> Merged at: 2015-03-08 21:37:18 UTC  
> Closed at: 2015-03-08 21:37:18 UTC  
> Url: https://github.com/boostorg/core/pull/9  

To update ticket# 10311

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-03-07 19:12:38 UTC  
> Url: https://github.com/boostorg/core/pull/9#issuecomment-77704714  

Wouldn't it be better if we use BOOST_WORKAROUND for consistency?

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-03-08 00:07:37 UTC  
> Url: https://github.com/boostorg/core/pull/9#issuecomment-77723649  

Frankly, I always found BOOST_WORKAROUND/BOOST_TESTED_AT confusing.

---

## Comment 3

> Username: pdimov  
> Created_at: 2015-03-08 03:00:07 UTC  
> Updated_at: 2015-03-08 03:01:20 UTC  
> Url: https://github.com/boostorg/core/pull/9#issuecomment-77729957  

In this case it would not use BOOST_TESTED_AT. `defined(__SUNPRO_CC) && BOOST_WORKAROUND(__SUNPRO_CC, <= 0x5120)`.  
  
The idea of BOOST_WORKAROUND is both to mark something as a workaround and to make it possible for all workarounds to be turned off (so that the supposedly conforming code is chosen.)  
  
In principle, the original should already have used BOOST_WORKAROUND, this time with BOOST_TESTED_AT, like `defined(__SUNPRO_CC) && BOOST_WORKAROUND(__SUNPRO_CC, BOOST_TESTED_AT(0x590))`. This says that all known versions need the workaround and the latest version tested was 5.9. When 5.13 is then discovered to no longer need the workaround, we replace BOOST_TESTED_AT with the appropriate <= test.

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-03-08 07:31:06 UTC  
> Url: https://github.com/boostorg/core/pull/9#issuecomment-77737173  

Thanks for the explanation. The problem is when I read the code I don't immediately know what `BOOST_WORKAROUND(__SUNPRO_CC, BOOST_TESTED_AT(0x590))` means. Is this condition true only for 0x590 version? What about other versions, are they not tested with the workaround? What in fact that means is 'always return `true` unless `BOOST_DETECT_OUTDATED_WORKAROUNDS` is defined'. So the version number doesn't really matter. This gives me a stutter.  
  
The sole `BOOST_WORKAROUND` is better in this respect because I can at least see the condition.

---

## Comment 5

> Username: pdimov  
> Created_at: 2015-03-08 12:12:53 UTC  
> Url: https://github.com/boostorg/core/pull/9#issuecomment-77745561  

Yes, you have to know what it means. But once you know, it's not really possible to misinterpret.

---
