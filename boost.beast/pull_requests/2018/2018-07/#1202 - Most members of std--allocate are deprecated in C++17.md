# #1202 Most members of std::allocate are deprecated in C++17 [Closed]

> Username: DanielaE  
> Created at: 2018-07-21 17:09:24 UTC  
> Updated at: 2018-07-24 05:37:31 UTC  
> Closed at: 2018-07-23 23:21:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1202  

Replace them by their cousins from std::allocator_traits. Without that, heaps of deprecation warnings will fall onto humble users when compiling with MSVC 15 in C++17 mode.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-21 17:23:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1202#issuecomment-406811098  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1202?src=pr&el=h1) Report  
> Merging [#1202](https://codecov.io/gh/boostorg/beast/pull/1202?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c4d1bfe7afc16ccf4ec3d6eae93082f810b6d2cb?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1202/graphs/tree.svg?token=Gq6MFA9JRF&width=650&src=pr&height=150)](https://codecov.io/gh/boostorg/beast/pull/1202?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1202   +/-   ##  
========================================  
  Coverage    95.52%   95.52%             
========================================  
  Files          113      113             
  Lines        11073    11073             
========================================  
  Hits         10578    10578             
  Misses         495      495  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1202?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1202?src=pr&el=footer). Last update [c4d1bfe...5f272ca](https://codecov.io/gh/boostorg/beast/pull/1202?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
