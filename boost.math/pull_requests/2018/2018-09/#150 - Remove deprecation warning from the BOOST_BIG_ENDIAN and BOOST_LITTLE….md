# #150 Remove deprecation warning from the BOOST_BIG_ENDIAN and BOOST_LITTLE… [Merged]

> Username: NAThompson  
> Created at: 2018-09-12 16:20:41 UTC  
> Updated at: 2018-09-15 13:41:01 UTC  
> Merged at: 2018-09-14 16:29:50 UTC  
> Closed at: 2018-09-14 16:29:50 UTC  
> Url: https://github.com/boostorg/math/pull/150  

…_ENDIAN macros.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-09-12 17:12:19 UTC  
> Url: https://github.com/boostorg/math/pull/150#issuecomment-420726417  

This patch doesn't look correct, even though Travis is passing with flying colors; `BOOST_ENDIAN_BIG_BYTE` and `BOOST_ENDIAN_LITTLE_BYTE` are always defined, to 0 or 1, so the proper way to check them is with `#if`, not `#ifdef`. See f.ex. https://github.com/boostorg/predef/blob/develop/include/boost/predef/detail/endian_compat.h

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-09-12 17:38:10 UTC  
> Url: https://github.com/boostorg/math/pull/150#issuecomment-420734970  

@pdimov : Nice catch, thanks!

---
