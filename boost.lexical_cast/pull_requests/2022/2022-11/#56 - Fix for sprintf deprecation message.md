# #56 Fix for sprintf deprecation message [Merged]

> Username: mborland  
> Created at: 2022-11-19 21:00:43 UTC  
> Updated at: 2023-01-17 12:25:41 UTC  
> Merged at: 2023-01-17 12:25:15 UTC  
> Closed at: 2023-01-17 12:25:15 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56  

Use same method as Boost.Assert (https://github.com/boostorg/assert/commit/601ee4ba7ae9d9b532dab5f7e757f4187dce6300)

---

## Comment 1

> Username: mborland  
> Created_at: 2022-11-21 08:51:52 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1321710972  

@apolukhin Can you please approve the CI run?

---

## Comment 2

> Username: mborland  
> Created_at: 2022-11-28 15:08:21 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1329265677  

Ping @apolukhin

---

## Comment 3

> Username: mclow  
> Created_at: 2022-11-30 00:24:42 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1331489848  

Is there a reason not to use `snprintf` all the time?  it was added (I think) in C99.

---

## Comment 4

> Username: mborland  
> Created_at: 2022-11-30 01:48:52 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1331541848  

> Is there a reason not to use `snprintf` all the time? it was added (I think) in C99.  
  
`snprintf` was changed for all cases in [this PR](https://github.com/boostorg/lexical_cast/pull/51), but was reverted because it broke support on some platforms.

---

## Review 5 [Commented]

> Username: mclow  
> Created_at: 2022-12-08 04:02:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/lexical_cast/pull/56#pullrequestreview-1209423314  

📁 include/boost/lexical_cast/detail/converter_lexical_streams.hpp

```diff
  82 | #endif
  83 | 
  84 | #if ( defined(_MSC_VER) && _MSC_VER < 1900 ) || ( defined(__MINGW32__) && !defined(__MINGW64_VERSION_MAJOR) )
```

> Username: mclow  
> Created_at: 2022-12-08 04:02:56 UTC  
> Updated_at: 2022-12-08 04:03:09 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#discussion_r1042901990  

Why not just use `snprintf` all the time?   
Is there some library that you're supporting that doesn't have `snprintf`?  
It was part of C99, and was available before that.

> Username: mborland  
> Created_at: 2022-12-08 04:08:28 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#discussion_r1042903879  

Unconditionally using `snprintf` was merged, but breaks support to [MinGW so was rolled back](https://github.com/boostorg/lexical_cast/pull/51#issuecomment-1236049443). This same snippet is used in Boost.Assert, and now Boost.Core so should be safe.

> Username: mclow  
> Created_at: 2022-12-08 04:19:05 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#discussion_r1042907736  

it appears that no one investigated why it failed, just "Well, it failed so roll back" in spite of @biodranik's assertion that "Mingw sources definitely have snprintf".

> Username: mborland  
> Created_at: 2022-12-08 04:24:59 UTC  
> Updated_at: 2022-12-08 04:25:00 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#discussion_r1042909986  

@pdimov will have a better answer than I since he is the original author. I am transplanting this snippet where needed because Apple Clang explodes with warnings when building or testing Boost.


---

## Comment 6

> Username: pdimov  
> Created_at: 2022-12-08 05:59:07 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1342100003  

You should probably wait for `boost::core::snprintf` from https://github.com/boostorg/core/pull/132 to get merged and then use that.

---

## Comment 7

> Username: pdimov  
> Created_at: 2022-12-09 01:01:50 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1343696063  

`boost/core/snprintf.hpp` has been added to Core develop.

---

## Comment 8

> Username: mborland  
> Created_at: 2022-12-09 02:34:10 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1343763851  

@pdimov Thanks. I have replaced the macros with `boost::core::snprintf`

---

## Comment 9

> Username: mborland  
> Created_at: 2022-12-09 15:48:59 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1344466892  

Pending further review this should be good to merge.

---

## Review 10 [Approved]

> Username: biodranik  
> Created_at: 2022-12-09 17:38:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/lexical_cast/pull/56#pullrequestreview-1211997025  

LGTM

📁 include/boost/lexical_cast/detail/converter_lexical_streams.hpp

```diff
  39 | #include <boost/detail/lcast_precision.hpp>
  40 | #include <boost/detail/workaround.hpp>
  41 |+ #include <boost/core/snprintf.hpp>
```

> Username: biodranik  
> Created_at: 2022-12-09 17:38:05 UTC  
> Updated_at: 2022-12-09 17:38:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#discussion_r1044675953  

Includes sorting is not used, right?

> Username: mborland  
> Created_at: 2022-12-09 18:17:41 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#discussion_r1044708142  

Correct


---

## Comment 11

> Username: mborland  
> Created_at: 2023-01-07 18:48:52 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1374572341  

Ping @apolukhin. There are now other linked projects that are looking for this change.

---

## Comment 12

> Username: apolukhin  
> Created_at: 2023-01-17 12:25:41 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/56#issuecomment-1385348958  

Many thanks to all envolved for the awesome work!

---
