# #159 [bit] Add support for `__builtin_bswap16` [Merged]

> Username: Lastique  
> Created at: 2023-11-25 12:43:29 UTC  
> Updated at: 2023-11-30 09:44:58 UTC  
> Merged at: 2023-11-30 09:36:21 UTC  
> Closed at: 2023-11-30 09:36:21 UTC  
> Url: https://github.com/boostorg/core/pull/159  



---

## Comment 1

> Username: pdimov  
> Created_at: 2023-11-25 12:48:43 UTC  
> Url: https://github.com/boostorg/core/pull/159#issuecomment-1826300578  

There's no need for that; all compilers recognize the pattern and turn it into a bswap16 if possible.

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2023-11-25 12:50:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/159#pullrequestreview-1748827157  

📁 include/boost/core/bit.hpp

```diff
  54 | 
  52 |- #if defined(BOOST_MSVC) && BOOST_MSVC >= 1926
  55 |+ #if !defined(BOOST_CORE_HAS_BUILTIN_BIT_CAST) && (defined(BOOST_MSVC) && BOOST_MSVC >= 1926)
```

> Username: pdimov  
> Created_at: 2023-11-25 12:50:11 UTC  
> Url: https://github.com/boostorg/core/pull/159#discussion_r1404875592  

Why is this change needed?

> Username: Lastique  
> Created_at: 2023-11-25 12:57:10 UTC  
> Url: https://github.com/boostorg/core/pull/159#discussion_r1404877542  

The `BOOST_CORE_HAS_BUILTIN_BIT_CAST` macro could be defined above, if MSVC supports `__has_builtin` one day. It's just a sanity check to avoid redefinition of the macro and a potential warning.  
  
It's not essential to the bswap16 change and hence is done in a separate commit. I can remove it if you want.


---

## Comment 3

> Username: Lastique  
> Created_at: 2023-11-25 12:59:37 UTC  
> Url: https://github.com/boostorg/core/pull/159#issuecomment-1826302325  

> There's no need for that; all compilers recognize the pattern and turn it into a bswap16 if possible.  
  
I'd rather be on the safe side and explicitly say what we want. The intrinsic is there, so why not use it.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-11-25 13:03:40 UTC  
> Url: https://github.com/boostorg/core/pull/159#issuecomment-1826303118  

It's not available on all targets and can be non-constexpr, so it's not an automatic "win".

---

## Comment 5

> Username: Lastique  
> Created_at: 2023-11-25 13:06:15 UTC  
> Updated_at: 2023-11-25 13:06:57 UTC  
> Url: https://github.com/boostorg/core/pull/159#issuecomment-1826303643  

> It's not available on all targets and can be non-constexpr, so it's not an automatic "win".  
  
Do you have an example of that? My understanding is that it is equivalent to bswap32/64, only in case of gcc it appeared a little later than the other two. Clang supported all three all along.  
  
PS: I've updated CI to fix clang failures.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-11-25 13:12:21 UTC  
> Url: https://github.com/boostorg/core/pull/159#issuecomment-1826304803  

I'll need to look that up. I'm not sure whether it still applies for the GCC versions we support nowadays.

---

## Comment 7

> Username: pdimov  
> Created_at: 2023-11-25 13:25:43 UTC  
> Url: https://github.com/boostorg/core/pull/159#issuecomment-1826308875  

Looks like it wasn't supported on GCC 4.7 and Clang 3.1, but this shouldn't be an issue because GCC 4.7 doesn't have `__has_builtin` so it's not going to get used anyway. (https://gcc.gnu.org/bugzilla/show_bug.cgi?id=52624)  
  
Appears to be constexpr when supported too.

---

## Comment 8

> Username: Lastique  
> Created_at: 2023-11-25 14:34:07 UTC  
> Url: https://github.com/boostorg/core/pull/159#issuecomment-1826347522  

Right, that's why I'm enabling it only since gcc 4.8.

---

## Review 9 [Commented]

> Username: pdimov  
> Created_at: 2023-11-25 16:21:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/159#pullrequestreview-1749211781  

📁 include/boost/core/bit.hpp

```diff
  57 | #endif
  58 | 
  59 |+ #if !defined(BOOST_CORE_HAS_BUILTIN_BSWAP16) && (defined(BOOST_GCC_VERSION) && BOOST_GCC_VERSION >= 40800)
```

> Username: pdimov  
> Created_at: 2023-11-25 16:21:20 UTC  
> Updated_at: 2023-11-25 16:21:21 UTC  
> Url: https://github.com/boostorg/core/pull/159#discussion_r1405161004  

Why BOOST_GCC_VERSION instead of BOOST_GCC?

> Username: pdimov  
> Created_at: 2023-11-26 01:05:36 UTC  
> Url: https://github.com/boostorg/core/pull/159#discussion_r1405282902  

Well? This looks OK to merge except for this bit.  
  
As far as I can see, `BOOST_GCC_VERSION` is mostly equivalent to `BOOST_GCC`, but is undocumented.  
  
Seems to me that if we want "real GCC 4.8 or above", this should be `BOOST_GCC`, and if we want "GCC-4.8-compatible", this should be `__GNUC__ * 100 + __GNUC_MINOR__ >= 408`.


---
