# #31 Expand Travis CI for more coverage [Merged]

> Username: jeking3  
> Created at: 2018-10-29 20:30:52 UTC  
> Updated at: 2018-10-30 10:55:21 UTC  
> Merged at: 2018-10-30 10:55:21 UTC  
> Closed at: 2018-10-30 10:55:21 UTC  
> Url: https://github.com/boostorg/rational/pull/31  

Expands coverage to match other CMT libraries.  
  
- gcc-4.6 was disabled due to an internal compiler error  
- UBSAN with gcc-8 exposing issues in Boost.Test, reported issue and fall back to gcc-7

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-30 01:09:17 UTC  
> Url: https://github.com/boostorg/rational/pull/31#issuecomment-434135747  

@pdimov, got an interesting failure here in that the gcc-8 UBSAN is failing, but the clang-6 and gcc-7 are not.  Any thoughts?  This was part of the expanded CI I am rolling out to all the CMT repos.

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-30 01:13:27 UTC  
> Url: https://github.com/boostorg/rational/pull/31#issuecomment-434136469  

This is a crash on exit in Boost.Test so it probably needs to be reported there.  
  
I think I saw that recently in some other repo too, don't remember which though.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-10-30 01:19:17 UTC  
> Updated_at: 2018-10-30 10:37:44 UTC  
> Url: https://github.com/boostorg/rational/pull/31#issuecomment-434137409  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/31?src=pr&el=h1) Report  
> Merging [#31](https://codecov.io/gh/boostorg/rational/pull/31?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/rational/commit/ec101994109702b650ae82aba714ed2280fa2ebc?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/31/graphs/tree.svg?width=650&token=GWRPyIttVH&height=150&src=pr)](https://codecov.io/gh/boostorg/rational/pull/31?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #31   +/-   ##  
========================================  
  Coverage    67.52%   67.52%             
========================================  
  Files            1        1             
  Lines          271      271             
  Branches        90       90             
========================================  
  Hits           183      183             
  Misses           3        3             
  Partials        85       85  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/31?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/31?src=pr&el=footer). Last update [ec10199...db0d8cf](https://codecov.io/gh/boostorg/rational/pull/31?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-30 01:23:29 UTC  
> Url: https://github.com/boostorg/rational/pull/31#issuecomment-434138189  

I did report it in a Boost.Test issue.  
What's curious is that only the gcc-8 UBSAN triggers it.  
I'm changing this repo to build with gcc-7 UBSAN for now.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-30 01:27:14 UTC  
> Url: https://github.com/boostorg/rational/pull/31#issuecomment-434138823  

Since it occurs during static destruction, it could be sensitive to destruction order, which may vary between compilers; or Boost.Test could do something differently for gcc-8.

---
