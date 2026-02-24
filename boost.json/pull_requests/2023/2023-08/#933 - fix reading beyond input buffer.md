# #933 fix reading beyond input buffer [Merged]

> Username: grisumbras  
> Created at: 2023-08-24 18:43:47 UTC  
> Updated at: 2023-08-25 18:05:46 UTC  
> Merged at: 2023-08-25 17:15:19 UTC  
> Closed at: 2023-08-25 17:15:19 UTC  
> Url: https://github.com/boostorg/json/pull/933  

This fixes a rare case when the parser first suspends inside a comment, then is given input exactly up to the newline character. Before the fix it proceeded to read past the end of the buffer or hit an assert.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-08-24 19:12:23 UTC  
> Updated_at: 2023-08-25 17:12:12 UTC  
> Url: https://github.com/boostorg/json/pull/933#issuecomment-1692267744  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#933](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2acdb29) into [develop](https://app.codecov.io/gh/boostorg/json/commit/f48b6dd4b0ea011feea3ca3311789b7fe47a1a22?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f48b6dd) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/933/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #933   +/-   ##  
========================================  
  Coverage    92.95%   92.95%             
========================================  
  Files           85       85             
  Lines         8047     8052    +5       
========================================  
+ Hits          7480     7485    +5       
  Misses         567      567             
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.44% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f48b6dd...2acdb29](https://app.codecov.io/gh/boostorg/json/pull/933?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-08-24 20:00:47 UTC  
> Url: https://github.com/boostorg/json/pull/933#issuecomment-1692329019  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/933/pullrequest-condensed-ee7ec7d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/933/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/933/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-08-25 18:05:46 UTC  
> Url: https://github.com/boostorg/json/pull/933#issuecomment-1693748025  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/933/pullrequest-condensed-07e396c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/933/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/933/pullrequest.html)

---
