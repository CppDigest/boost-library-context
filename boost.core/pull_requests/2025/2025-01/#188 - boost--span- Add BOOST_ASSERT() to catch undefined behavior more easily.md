# #188 boost::span: Add BOOST_ASSERT() to catch undefined behavior more easily [Closed]

> Username: SoapGentoo  
> Created at: 2025-01-11 14:57:37 UTC  
> Updated at: 2025-01-19 01:26:39 UTC  
> Closed at: 2025-01-18 12:47:26 UTC  
> Url: https://github.com/boostorg/core/pull/188  

@glenfe @pdimov

---

## Comment 1

> Username: glenfe  
> Created_at: 2025-01-17 00:57:21 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2597211599  

@SoapGentoo See https://github.com/boostorg/core/commit/b0fc7396f91cc8ee754f3dcc41dfa045aee81d3a on branch feature/span. I wanted to see if not losing C++11 constexpr was possible.  
  
I'll add more constexpr tests. The initial ones are just to cover the new asserts proposed.

---

## Comment 2

> Username: SoapGentoo  
> Created_at: 2025-01-17 10:14:30 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2597893126  

@glenfe sure, that's fine too

---

## Comment 3

> Username: SoapGentoo  
> Created_at: 2025-01-17 10:18:48 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2597902257  

@glenfe is it legal to call some form of `assert()` in C++11 constexpr?

---

## Comment 4

> Username: glenfe  
> Created_at: 2025-01-17 10:24:32 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2597913183  

@SoapGentoo This would be usable in C++11 constexpr:  
  
```  
#ifdef NDEBUG  
#define BOOST_CORE_ASSERT(c) void(0)  
#else  
#define BOOST_CORE_ASSERT(c) ((c) ? void(0) : []{ assert(!#c); }())  
#endif  
```  
  
Looking into other approaches too.

---

## Comment 5

> Username: glenfe  
> Created_at: 2025-01-18 12:47:26 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2599703446  

@SoapGentoo Merged 24a8174ef1c9b56226e5478ad65a913e28b79bda and a subsequent commit after the tests passed on the feature branch. The idea is to now see what else breaks, if any, before adding more.  
  
Eventually, I would just replace `BOOST_CORE_ASSERT` with `BOOST_ASSERT` after we no longer support GCC 4.x or older.

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-01-18 16:03:54 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2599769962  

I'm not sure I like this that much. It foregoes the use of BOOST_ASSERT for everyone just because GCC 4.x/5 doesn't support it in constexpr, but the number of users of constexpr spans under GCC 4.x/5 -std=c++11 is zero and will remain zero.  
  
Plus, it makes available a macro (BOOST_CORE_ASSERT) that sounds and looks like a recommended replacement for BOOST_ASSERT, and it definitely isn't one.

---

## Comment 7

> Username: Lastique  
> Created_at: 2025-01-18 17:59:38 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2599810128  

If the macro is not public, it should definitely have `DETAIL` or something like that in the name.  
  
And I agree with Peter that we don't need another public assert facility.

---

## Comment 8

> Username: glenfe  
> Created_at: 2025-01-18 19:18:04 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2599847043  

No disagreement about the name. It was intended to be private. Renamed accordingly.

---

## Comment 9

> Username: pdimov  
> Created_at: 2025-01-18 20:09:32 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2599861811  

I think that this should just use BOOST_ASSERT and disable the constexpr tests for GCC 4.x and 5/c++11, as there's essentially zero demand for C++11 constexpr use of span (if you try actually writing constexpr code using spans you'll see that C++11 is completely unworkable.)  
  
But failing that, the detail macro should fall back to BOOST_ASSERT when the compiler isn't GCC 4.x or 5.  
  
I'm not sure whether avoiding Config here makes any practical sense, but I suppose that's a matter of taste. I'd just use BOOST_WORKAROUND(BOOST_GCC, < 60000) instead.

---

## Comment 10

> Username: glenfe  
> Created_at: 2025-01-18 22:20:17 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2600191592  

It's not just the constexpr tests that would fail on GCC 4.x it's also the regular span tests.   
The other alternative I'm OK with is using BOOST_ASSERT but disabling assertions on GCC 4. i.e. 787b03ea9ca35089cecb80c5f2ca37e10046fbaf on branch https://github.com/boostorg/core/compare/feature/span

---

## Comment 11

> Username: pdimov  
> Created_at: 2025-01-18 23:11:25 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2600271750  

What I do in Boost.Array is remove the `constexpr` for 4.x: https://github.com/boostorg/array/blob/01983ff60439e0e0b1defdab3ca6c068be513472/include/boost/array.hpp#L118-L124  
  
Not sure why GCC 5 is OK with that there, but isn't here.

---

## Comment 12

> Username: pdimov  
> Created_at: 2025-01-18 23:12:40 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2600272618  

Probably because it isn't tested yet. :-)

---

## Comment 13

> Username: pdimov  
> Created_at: 2025-01-19 01:26:37 UTC  
> Url: https://github.com/boostorg/core/pull/188#issuecomment-2600379400  

In Boost.Array's case, I had already written a test for the asserts  
  
https://github.com/boostorg/array/blob/01983ff60439e0e0b1defdab3ca6c068be513472/test/array_access_test.cpp#L55  
  
so removing the asserts for GCC 4.x actually broke it and I decided to remove the constexpr instead.

---
