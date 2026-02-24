# #88 - BOOST_LOCALE_HAVE_WCSFTIME_L unused [Closed]

> Username: Flamefire  
> Created at: 2022-06-02 09:01:48 UTC  
> Updated at: 2022-10-23 12:09:43 UTC  
> Closed at: 2022-10-23 12:09:43 UTC  
> Url: https://github.com/boostorg/locale/issues/88  

The define at https://github.com/boostorg/locale/blob/b889b35645159e7ca9fa7d4e5ef32532fdb1a123/src/posix/numeric.cpp#L35 is not used.  
  
Instead the non-prefixed one is used at https://github.com/boostorg/locale/blob/b889b35645159e7ca9fa7d4e5ef32532fdb1a123/src/posix/numeric.cpp#L125  
  
@artyom-beilis Do you know which test should detect this problem?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2022-06-02 11:23:21 UTC  
> Url: https://github.com/boostorg/locale/issues/88#issuecomment-1144750715  

Interesting and good question.  
  
This is generic test of `posix_formatting` IIRC.  
But it looks like that fallback is working good enough so it wasn't found.
