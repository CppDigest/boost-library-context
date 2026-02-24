# #1573 Fix compilation on MSVC with std::string_view [Closed]

> Username: djarek  
> Created at: 2019-04-13 16:16:28 UTC  
> Updated at: 2019-04-16 17:20:52 UTC  
> Closed at: 2019-04-15 16:04:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1573  

std::string_view::iterator is not necessarily a raw pointer.  
Resolves: #1572

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-13 16:31:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1573#issuecomment-482833289  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1573?src=pr&el=h1) Report  
> Merging [#1573](https://codecov.io/gh/boostorg/beast/pull/1573?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0345aa194ddfd2b24fd0d0539798dd672e076772?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1573/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1573?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1573   +/-   ##  
========================================  
  Coverage    92.61%   92.61%             
========================================  
  Files          150      150             
  Lines        12102    12102             
========================================  
  Hits         11208    11208             
  Misses         894      894  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1573?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/boostorg/beast/pull/1573/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/detail/rfc7230.ipp](https://codecov.io/gh/boostorg/beast/pull/1573/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmlwcA==) | `90% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1573?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1573?src=pr&el=footer). Last update [0345aa1...2ed7776](https://codecov.io/gh/boostorg/beast/pull/1573?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
