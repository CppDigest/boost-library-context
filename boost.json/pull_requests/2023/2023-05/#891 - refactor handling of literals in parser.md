# #891 refactor handling of literals in parser [Closed]

> Username: grisumbras  
> Created at: 2023-05-18 10:59:05 UTC  
> Updated at: 2023-05-19 19:25:58 UTC  
> Closed at: 2023-05-19 19:25:55 UTC  
> Url: https://github.com/boostorg/json/pull/891  

This PR will be particularly relevant, when we add handling of non-standard literals Infinity, -Infinity, and NaN.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-18 12:16:12 UTC  
> Updated_at: 2023-05-18 12:27:28 UTC  
> Url: https://github.com/boostorg/json/pull/891#issuecomment-1552968565  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#891](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9b9354b) into [develop](https://app.codecov.io/gh/boostorg/json/commit/7053124c57220793d1e11868f835e01c506550ea?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7053124) will **decrease** coverage by `0.01%`.  
> The diff coverage is `94.73%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/891/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #891      +/-   ##  
===========================================  
- Coverage    99.19%   99.18%   -0.01%       
===========================================  
  Files           70       70                
  Lines         6851     6783      -68       
===========================================  
- Hits          6796     6728      -68       
  Misses          55       55                
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.23% <94.73%> (-0.10%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7053124...9b9354b](https://app.codecov.io/gh/boostorg/json/pull/891?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-05-18 13:15:36 UTC  
> Url: https://github.com/boostorg/json/pull/891#issuecomment-1553040480  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/891/pullrequest-condensed-4e17613.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/891/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/891/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-05-19 19:25:51 UTC  
> Url: https://github.com/boostorg/json/pull/891#issuecomment-1555131875  

Moved to #894

---
