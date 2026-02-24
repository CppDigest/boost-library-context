# #62 Added CI framework [Merged]

> Username: jeking3  
> Created at: 2018-07-19 19:50:37 UTC  
> Updated at: 2018-07-30 13:14:59 UTC  
> Merged at: 2018-07-30 13:14:57 UTC  
> Closed at: 2018-07-30 13:14:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/62  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
      - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
      - README, LICENSE, etc.  
  
Additional fixes for cygwin builds to work.  
This is the same framework being used on most other CMT repositories.  
Also fixes a bug or two found along the way.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-20 02:19:17 UTC  
> Updated_at: 2018-07-23 19:16:46 UTC  
> Url: https://github.com/boostorg/iostreams/pull/62#issuecomment-406469044  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/62?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@2303395`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `0%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/62/graphs/tree.svg?token=LBEfwtNfca&width=650&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/62?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #62   +/-   ##  
==========================================  
  Coverage           ?   64.71%             
==========================================  
  Files              ?       81             
  Lines              ?     3545             
  Branches           ?      950             
==========================================  
  Hits               ?     2294             
  Misses             ?      461             
  Partials           ?      790  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/62?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/mapped\_file.cpp](https://codecov.io/gh/boostorg/iostreams/pull/62/diff?src=pr&el=tree#diff-c3JjL21hcHBlZF9maWxlLmNwcA==) | `49.33% <0%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/62?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/62?src=pr&el=footer). Last update [2303395...f9c8ecd](https://codecov.io/gh/boostorg/iostreams/pull/62?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
