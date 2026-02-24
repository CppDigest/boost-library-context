# #21 Removed expected failure for stl_concept_covering [Merged]

> Username: Kojoley  
> Created at: 2019-02-19 01:45:22 UTC  
> Updated at: 2019-02-19 12:15:53 UTC  
> Merged at: 2019-02-19 12:09:34 UTC  
> Closed at: 2019-02-19 12:09:34 UTC  
> Url: https://github.com/boostorg/concept_check/pull/21  

It was marked for all toolsets for some reason.  
https://github.com/boostorg/boost/commit/741a03ba0b66330be2b18860175ebcb5d13b3971

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-02-19 11:32:29 UTC  
> Updated_at: 2019-02-19 11:35:10 UTC  
> Url: https://github.com/boostorg/concept_check/pull/21#issuecomment-465094950  

# [Codecov](https://codecov.io/gh/boostorg/concept_check/pull/21?src=pr&el=h1) Report  
> Merging [#21](https://codecov.io/gh/boostorg/concept_check/pull/21?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/concept_check/commit/7dbaa8541362bb2011f766cf16e6d1f0169af375?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/concept_check/pull/21/graphs/tree.svg?width=650&token=4FWH598KON&height=150&src=pr)](https://codecov.io/gh/boostorg/concept_check/pull/21?src=pr&el=tree)  
  
```diff  
@@          Coverage Diff           @@  
##           develop    #21   +/-   ##  
======================================  
  Coverage      100%   100%             
======================================  
  Files            1      1             
  Lines            2      2             
======================================  
  Hits             2      2  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/concept_check/pull/21?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/concept_check/pull/21?src=pr&el=footer). Last update [7dbaa85...782d90d](https://codecov.io/gh/boostorg/concept_check/pull/21?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-02-19 11:57:55 UTC  
> Url: https://github.com/boostorg/concept_check/pull/21#issuecomment-465101718  

Not surprising, the entire test had been disabled for most compilers until July 2018.  See: https://github.com/boostorg/concept_check/commit/376201b10db75b1878a9a807657d10ef997e4c21#diff-c826f3e1a4cea4575c2753500fc63f5a (line 7)

---
