# #68 Add move ctor and assignment and swap functions to fstream and filebuf [Merged]

> Username: Flamefire  
> Created at: 2020-02-19 13:35:50 UTC  
> Updated at: 2020-03-03 14:52:24 UTC  
> Merged at: 2020-03-03 14:52:20 UTC  
> Closed at: 2020-03-03 14:52:20 UTC  
> Url: https://github.com/boostorg/nowide/pull/68  

Makes them compatible with their post C++11 std counterparts based on whether rvalues are supported  
  
Closes #18

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-28 10:20:06 UTC  
> Updated_at: 2020-03-03 14:15:28 UTC  
> Url: https://github.com/boostorg/nowide/pull/68#issuecomment-592447855  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/68?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@959d3d0`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `76.74%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/nowide/pull/68/graphs/tree.svg?width=650&token=orhMugjCha&height=150&src=pr)](https://codecov.io/gh/boostorg/nowide/pull/68?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #68   +/-   ##  
==========================================  
  Coverage           ?   70.33%             
==========================================  
  Files              ?       12             
  Lines              ?      590             
  Branches           ?      190             
==========================================  
  Hits               ?      415             
  Misses             ?       65             
  Partials           ?      110  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/68?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/fstream.hpp](https://codecov.io/gh/boostorg/nowide/pull/68/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZnN0cmVhbS5ocHA=) | `78.87% <100%> (ø)` | |  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/68/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `55.25% <52.38%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/nowide/pull/68?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/nowide/pull/68?src=pr&el=footer). Last update [959d3d0...0d88794](https://codecov.io/gh/boostorg/nowide/pull/68?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
