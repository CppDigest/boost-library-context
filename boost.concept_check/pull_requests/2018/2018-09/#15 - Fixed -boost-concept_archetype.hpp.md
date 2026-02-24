# #15 Fixed <boost/concept_archetype.hpp> [Merged]

> Username: CromwellEnage  
> Created at: 2018-09-20 14:22:48 UTC  
> Updated at: 2018-09-21 17:37:42 UTC  
> Merged at: 2018-09-21 17:37:42 UTC  
> Closed at: 2018-09-21 17:37:42 UTC  
> Url: https://github.com/boostorg/concept_check/pull/15  

Added missing #include <boost/config/workaround.hpp> which defines BOOST_WORKAROUND.  
  
Without this statement, a fresh fork of Boost.Graph was failing tests on Travis Cl.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-09-20 17:57:48 UTC  
> Url: https://github.com/boostorg/concept_check/pull/15#issuecomment-423276812  

# [Codecov](https://codecov.io/gh/boostorg/concept_check/pull/15?src=pr&el=h1) Report  
> Merging [#15](https://codecov.io/gh/boostorg/concept_check/pull/15?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/concept_check/commit/8847e4b10412b12690547f7f61360cbeaf3d8706?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/concept_check/pull/15/graphs/tree.svg?width=650&token=4FWH598KON&height=150&src=pr)](https://codecov.io/gh/boostorg/concept_check/pull/15?src=pr&el=tree)  
  
```diff  
@@          Coverage Diff           @@  
##           develop    #15   +/-   ##  
======================================  
  Coverage      100%   100%             
======================================  
  Files            1      1             
  Lines            2      2             
======================================  
  Hits             2      2  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/concept_check/pull/15?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/concept_check/pull/15?src=pr&el=footer). Last update [8847e4b...ddcfac9](https://codecov.io/gh/boostorg/concept_check/pull/15?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
