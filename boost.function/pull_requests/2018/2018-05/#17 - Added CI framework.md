# #17 Added CI framework [Merged]

> Username: jeking3  
> Created at: 2018-05-16 01:19:45 UTC  
> Updated at: 2018-05-27 14:14:59 UTC  
> Merged at: 2018-05-27 14:14:56 UTC  
> Closed at: 2018-05-27 14:14:56 UTC  
> Url: https://github.com/boostorg/function/pull/17  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
      - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
      - README, LICENSE, etc.  
  
AppVeyor results: https://ci.appveyor.com/project/jeking3/function/build/1.0.3-develop  
(mingw builds passed in previous build: https://ci.appveyor.com/project/jeking3/function/build/1.0.2-develop)  
Travis results: https://travis-ci.org/jeking3/function/builds/380948645  
  
After merging this, Appveyor builds need to be enabled for the repository.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-05-16 01:51:03 UTC  
> Updated_at: 2018-05-27 14:14:12 UTC  
> Url: https://github.com/boostorg/function/pull/17#issuecomment-389369113  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/17?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@52fae95`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/17/graphs/tree.svg?width=650&height=150&src=pr&token=beMMliwLU7)](https://codecov.io/gh/boostorg/function/pull/17?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #17   +/-   ##  
==========================================  
  Coverage           ?   53.76%             
==========================================  
  Files              ?        3             
  Lines              ?      385             
  Branches           ?       98             
==========================================  
  Hits               ?      207             
  Misses             ?       85             
  Partials           ?       93  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/17?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/17?src=pr&el=footer). Last update [52fae95...23c1add](https://codecov.io/gh/boostorg/function/pull/17?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-05-19 18:09:59 UTC  
> Url: https://github.com/boostorg/function/pull/17#issuecomment-390422735  

@mclow this is successful on all builds so it can be merged.  Need to enable appveyor for the repository.

---
