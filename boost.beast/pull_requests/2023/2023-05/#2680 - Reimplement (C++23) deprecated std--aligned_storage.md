# #2680 Reimplement (C++23) deprecated std::aligned_storage [Merged]

> Username: ecatmur  
> Created at: 2023-05-10 14:42:41 UTC  
> Updated at: 2023-05-15 01:15:52 UTC  
> Merged at: 2023-05-15 01:03:10 UTC  
> Closed at: 2023-05-15 01:03:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2680  

cf. https://github.com/boostorg/beast/issues/2535

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-05-10 23:18:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2680#issuecomment-1542925828  

Why not just use `boost::aligned_storage` ?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-05-12 11:38:01 UTC  
> Updated_at: 2023-05-12 11:38:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2680#issuecomment-1545607362  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2680](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (90e1cc2) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/b0996f099ac4cb024675b0b320f07e2297c3130c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b0996f0) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2680/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2680   +/-   ##  
========================================  
  Coverage    92.96%   92.97%             
========================================  
  Files          177      177             
  Lines        13651    13658    +7       
========================================  
+ Hits         12691    12698    +7       
  Misses         960      960             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/type\_traits.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/detail/decorator.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2RlY29yYXRvci5ocHA=) | `91.46% <ø> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2680/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b0996f0...90e1cc2](https://app.codecov.io/gh/boostorg/beast/pull/2680?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
