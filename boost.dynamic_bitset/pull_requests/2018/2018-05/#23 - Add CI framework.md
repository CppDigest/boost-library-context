# #23 Add CI framework [Merged]

> Username: jeking3  
> Created at: 2018-05-17 17:23:36 UTC  
> Updated at: 2018-07-05 01:54:37 UTC  
> Merged at: 2018-07-04 11:49:12 UTC  
> Closed at: 2018-07-04 11:49:12 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/23  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-04 01:55:31 UTC  
> Updated_at: 2018-07-04 04:13:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/23#issuecomment-402340235  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/23?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@4f54d83`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/23/graphs/tree.svg?src=pr&token=PVG5jth1ez&width=650&height=150)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/23?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #23   +/-   ##  
==========================================  
  Coverage           ?   75.41%             
==========================================  
  Files              ?        5             
  Lines              ?      541             
  Branches           ?      198             
==========================================  
  Hits               ?      408             
  Misses             ?       23             
  Partials           ?      110  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/23?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/23?src=pr&el=footer). Last update [4f54d83...708c46b](https://codecov.io/gh/boostorg/dynamic_bitset/pull/23?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mclow  
> Created_at: 2018-07-04 18:41:29 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/23#issuecomment-402541119  

My assumption is that you're planning merging this into `master` after the 1.68.0 beta.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-07-05 01:20:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/23#issuecomment-402580505  

We can wait if you prefer.  It's pretty low risk as you can see, and fixes the tests so they run properly on all the CI platforms.

---

## Comment 4

> Username: mclow  
> Created_at: 2018-07-05 01:54:37 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/23#issuecomment-402584388  

There's no reason to hustle it into the beta - that's my thought.

---
