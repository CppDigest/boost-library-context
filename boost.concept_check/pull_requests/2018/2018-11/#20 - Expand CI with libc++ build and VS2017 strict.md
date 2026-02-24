# #20 Expand CI with libc++ build and VS2017 strict [Merged]

> Username: jeking3  
> Created at: 2018-11-03 03:49:26 UTC  
> Updated at: 2018-11-06 13:56:05 UTC  
> Merged at: 2018-11-06 13:54:54 UTC  
> Closed at: 2018-11-06 13:54:54 UTC  
> Url: https://github.com/boostorg/concept_check/pull/20  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-03 20:07:45 UTC  
> Updated_at: 2018-11-05 01:50:58 UTC  
> Url: https://github.com/boostorg/concept_check/pull/20#issuecomment-435617099  

# [Codecov](https://codecov.io/gh/boostorg/concept_check/pull/20?src=pr&el=h1) Report  
> Merging [#20](https://codecov.io/gh/boostorg/concept_check/pull/20?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/concept_check/commit/c2723f984ccb028228ea7015ac81d840cb9b7c9b?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/concept_check/pull/20/graphs/tree.svg?width=650&token=4FWH598KON&height=150&src=pr)](https://codecov.io/gh/boostorg/concept_check/pull/20?src=pr&el=tree)  
  
```diff  
@@          Coverage Diff           @@  
##           develop    #20   +/-   ##  
======================================  
  Coverage      100%   100%             
======================================  
  Files            1      1             
  Lines            2      2             
======================================  
  Hits             2      2  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/concept_check/pull/20?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/concept_check/pull/20?src=pr&el=footer). Last update [c2723f9...3439cde](https://codecov.io/gh/boostorg/concept_check/pull/20?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2018-11-05 12:49:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/concept_check/pull/20#pullrequestreview-171539271  

📁 test/stl_concept_covering.cpp

```diff
  79 | }
  80 | 
  81 |+ // for std::shuffle
```

> Username: jeking3  
> Created_at: 2018-11-05 12:49:40 UTC  
> Updated_at: 2018-11-05 12:49:41 UTC  
> Url: https://github.com/boostorg/concept_check/pull/20#discussion_r230737435  

@pdimov I'm guessing I could have done this using archetypes and not an implementation of the URBG concept, but this is a new concept for me (pun not intended).


---
