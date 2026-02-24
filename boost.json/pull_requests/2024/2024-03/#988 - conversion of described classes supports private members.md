# #988 conversion of described classes supports private members [Merged]

> Username: grisumbras  
> Created at: 2024-03-01 19:01:38 UTC  
> Updated at: 2024-03-02 09:17:07 UTC  
> Merged at: 2024-03-02 08:38:16 UTC  
> Closed at: 2024-03-02 08:38:16 UTC  
> Url: https://github.com/boostorg/json/pull/988  

Fix #986

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-01 20:17:07 UTC  
> Url: https://github.com/boostorg/json/pull/988#issuecomment-1973851757  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest-condensed-b2117fc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-03-01 22:32:06 UTC  
> Url: https://github.com/boostorg/json/pull/988#issuecomment-1974023122  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest-condensed-141fa26.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-03-02 08:33:00 UTC  
> Updated_at: 2024-03-02 08:33:03 UTC  
> Url: https://github.com/boostorg/json/pull/988#issuecomment-1974712339  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.08%. Comparing base [(`8f5b635`)](https://app.codecov.io/gh/boostorg/json/commit/8f5b63510b07caa96f413777a412d930781fac5d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2883ff1`)](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/988/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #988   +/-   ##  
========================================  
  Coverage    93.08%   93.08%             
========================================  
  Files           87       87             
  Lines         8125     8125             
========================================  
  Hits          7563     7563             
  Misses         562      562             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/parse\_into.hpp](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9wYXJzZV9pbnRvLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_from.hpp](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/conversion.hpp](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8f5b635...2883ff1](https://app.codecov.io/gh/boostorg/json/pull/988?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-03-02 09:17:07 UTC  
> Url: https://github.com/boostorg/json/pull/988#issuecomment-1974742758  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest-condensed-c9bf8bf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/988/pullrequest.html)

---
