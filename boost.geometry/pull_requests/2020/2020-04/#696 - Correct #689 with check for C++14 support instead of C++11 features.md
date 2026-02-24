# #696 Correct #689 with check for C++14 support instead of C++11 features [Merged]

> Username: mloskot  
> Created at: 2020-04-14 21:01:09 UTC  
> Updated at: 2020-04-15 16:59:01 UTC  
> Merged at: 2020-04-15 16:58:21 UTC  
> Closed at: 2020-04-15 16:58:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/696  

Refines #689  
  
------  
  
Thanks to @Mike-Devel for pointing that out in https://github.com/boostorg/geometry/pull/689#issuecomment-613523835

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-04-15 09:14:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#issuecomment-613920062  

@awulkiew Do you have any comments to this or can we merge?

---

## Review 2 [Commented]

> Username: Mike-Devel  
> Created_at: 2020-04-15 09:34:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/696#pullrequestreview-393610849  

📁 include/boost/geometry/geometry.hpp

```diff
  23 |+ #if !defined(BOOST_GEOMETRY_DISABLE_DEPRECATED_03_WARNING)
  24 | #include <boost/config.hpp>
  25 |+ #if defined(BOOST_NO_CXX14_CONSTEXPR) || (__cplusplus < 201402L)
```

> Username: Mike-Devel  
> Created_at: 2020-04-15 09:34:32 UTC  
> Updated_at: 2020-04-15 09:46:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#discussion_r408709247  

This will evaluate to true on all versions of MSVC by default (you have to set a special compile switch for MSVC to define a "correct" value for `__cplusplus`: https://docs.microsoft.com/en-us/cpp/build/reference/zc-cplusplus?view=vs-2019).   
  
I thought the conclusion in the other thread was to just check for `BOOST_NO_CXX14_CONSTEXPR`?

> Username: mloskot  
> Created_at: 2020-04-15 09:43:48 UTC  
> Updated_at: 2020-04-15 09:46:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#discussion_r408714912  

I meant to use `_MSVC_LANG` in stead of  `__cplusplus`. I'll remove that sub-condition.  
  
> I thought the conclusion in the other thread was to just check for BOOST_NO_CXX14_CONSTEXPR?  
  
Yes, that was the plan. I'll update it.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2020-04-15 11:31:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/696#pullrequestreview-393687694  

📁 include/boost/geometry/geometry.hpp

```diff
  26 |- #if !defined(BOOST_GEOMETRY_DISABLE_DEPRECATED_03_WARNING)
  27 |- BOOST_PRAGMA_MESSAGE("CAUTION: Boost.Geometry in Boost 1.73 deprecates support for the C++03 and will require C++14 from Boost 1.75 onwards.")
  27 |+ BOOST_PRAGMA_MESSAGE("CAUTION: Boost.Geometry in Boost 1.73 deprecates support for C++03 and will require C++14 from Boost 1.75 onwards.")
```

> Username: barendgehrels  
> Created_at: 2020-04-15 11:30:49 UTC  
> Updated_at: 2020-04-15 11:31:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#discussion_r408772474  

It is possible to use Boost.Geometry without including `geometry.hpp` - we do that often ourselves in unit tests. And in the examples.  
But in practice it is probably nearly always included like this.  
  
So I'm OK with this place - but we might also place it in `boost/geometry/core/config.hpp` (including `boost/config.hpp` already)

> Username: awulkiew  
> Created_at: 2020-04-15 15:44:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#discussion_r408944862  

That's a good idea, though `core/config.hpp` is included only by a handful of files basically related to `get_turns` and tuples handling. So a different core file could be used, e.g. `core/tags.hpp` which at first glance (https://github.com/awulkiew/cxx-dependency-visualizer) is one of the most included bottom-level headers.

> Username: mloskot  
> Created_at: 2020-04-15 16:58:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#discussion_r408993493  

I think we can keep it in `geometry.hpp`. There will be also docs, README, and other means to announce.


---

## Comment 4

> Username: barendgehrels  
> Created_at: 2020-04-15 11:31:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#issuecomment-613983290  

Thanks, I'm basically OK

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-04-15 16:59:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/696#issuecomment-614158686  

Remaining question is if/when to schedule develop to master merge for Boost 1.73.  
How that is going to be handled?

---
