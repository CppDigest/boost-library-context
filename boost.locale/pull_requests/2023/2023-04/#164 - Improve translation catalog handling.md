# #164 [Refactor] Improve translation catalog handling [Merged]

> Username: Flamefire  
> Created at: 2023-04-24 19:56:34 UTC  
> Updated at: 2023-04-25 18:08:58 UTC  
> Merged at: 2023-04-25 18:08:54 UTC  
> Closed at: 2023-04-25 18:08:54 UTC  
> Url: https://github.com/boostorg/locale/pull/164  

Combine 3 vectors of message domain data into one  
  
Instead of having to synchronize the size of 3 related vectors use a vector of structs with all the data so it is clear that all data is available if any is.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-04-25 04:22:13 UTC  
> Url: https://github.com/boostorg/locale/pull/164#issuecomment-1521135678  

## [Codecov](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#164](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e71aace) into [develop](https://codecov.io/gh/boostorg/locale/commit/cc075982c03362c5c38d7de95593b3e66ce65995?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cc07598) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/164/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #164   +/-   ##  
========================================  
  Coverage    90.69%   90.69%             
========================================  
  Files          111      111             
  Lines         9654     9654             
========================================  
  Hits          8756     8756             
  Misses         898      898             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/util/encoding.cpp](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2VuY29kaW5nLmNwcA==) | `92.00% <ø> (ø)` | |  
| [src/boost/locale/shared/message.cpp](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbWVzc2FnZS5jcHA=) | `96.64% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cc07598...e71aace](https://codecov.io/gh/boostorg/locale/pull/164?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
