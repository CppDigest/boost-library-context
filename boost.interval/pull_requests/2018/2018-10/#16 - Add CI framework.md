# #16 Add CI framework [Closed]

> Username: jeking3  
> Created at: 2018-10-30 14:27:27 UTC  
> Updated at: 2018-10-31 13:33:20 UTC  
> Closed at: 2018-10-31 13:33:19 UTC  
> Url: https://github.com/boostorg/interval/pull/16  

Adds:  
  - travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  
Notes:  
  - clang does not provide the floating point rounding support needed to pass tests, so clang is not tested in CI.  
  - valgrind tests are failing even with `-frounding-math` - need to understand why...

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-10-31 12:26:16 UTC  
> Updated_at: 2018-10-31 13:32:41 UTC  
> Url: https://github.com/boostorg/interval/pull/16#issuecomment-434667809  

# [Codecov](https://codecov.io/gh/boostorg/interval/pull/16?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@88944ea`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/interval/pull/16/graphs/tree.svg?width=650&token=GejkQK9wH8&height=150&src=pr)](https://codecov.io/gh/boostorg/interval/pull/16?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #16   +/-   ##  
==========================================  
  Coverage           ?   57.01%             
==========================================  
  Files              ?       22             
  Lines              ?      984             
  Branches           ?      408             
==========================================  
  Hits               ?      561             
  Misses             ?      189             
  Partials           ?      234  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/interval/pull/16?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/interval/pull/16?src=pr&el=footer). Last update [88944ea...5431326](https://codecov.io/gh/boostorg/interval/pull/16?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-10-31 13:33:19 UTC  
> Url: https://github.com/boostorg/interval/pull/16#issuecomment-434688224  

Will re-open, going to straighten things out using my fork's build resources first.

---
