# #24 Fix gcc 9 deprecated copy warnings in antistable. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2019-03-22 10:38:20 UTC  
> Updated at: 2019-03-30 11:43:44 UTC  
> Merged at: 2019-03-30 11:43:36 UTC  
> Closed at: 2019-03-30 11:43:36 UTC  
> Url: https://github.com/boostorg/move/pull/24  

Hi,  
  
Without this, when using boost container's flat_map/flat_set, I am having depecated copy warnings in gcc 9. If I declare the copy constructor private (or deleted) then it does not compile anymore, so I assume copy constructor is really needed.  
  
Cheers,  
Romain

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-22 11:24:40 UTC  
> Updated_at: 2019-03-22 11:27:35 UTC  
> Url: https://github.com/boostorg/move/pull/24#issuecomment-475584473  

# [Codecov](https://codecov.io/gh/boostorg/move/pull/24?src=pr&el=h1) Report  
> Merging [#24](https://codecov.io/gh/boostorg/move/pull/24?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/move/commit/756c964798ff1110406fab541b85267aefb6fb6c?src=pr&el=desc) will **decrease** coverage by `0.52%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/move/pull/24/graphs/tree.svg?width=650&token=dMUaS9WR0b&height=150&src=pr)](https://codecov.io/gh/boostorg/move/pull/24?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #24      +/-   ##  
===========================================  
- Coverage     72.1%   71.57%   -0.53%       
===========================================  
  Files           28       28                
  Lines         1814     1819       +5       
  Branches       612      623      +11       
===========================================  
- Hits          1308     1302       -6       
- Misses         127      130       +3       
- Partials       379      387       +8  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/move/pull/24?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/move/algo/predicate.hpp](https://codecov.io/gh/boostorg/move/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2FsZ28vcHJlZGljYXRlLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/move/algo/detail/merge.hpp](https://codecov.io/gh/boostorg/move/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2FsZ28vZGV0YWlsL21lcmdlLmhwcA==) | `79.53% <0%> (-3.18%)` | :arrow_down: |  
| [...ude/boost/move/algo/detail/adaptive\_sort\_merge.hpp](https://codecov.io/gh/boostorg/move/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2FsZ28vZGV0YWlsL2FkYXB0aXZlX3NvcnRfbWVyZ2UuaHBw) | `64.16% <0%> (+0.13%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/move/pull/24?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/move/pull/24?src=pr&el=footer). Last update [756c964...68d57d8](https://codecov.io/gh/boostorg/move/pull/24?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2019-03-30 11:43:44 UTC  
> Url: https://github.com/boostorg/move/pull/24#issuecomment-478238070  

Thanks

---
