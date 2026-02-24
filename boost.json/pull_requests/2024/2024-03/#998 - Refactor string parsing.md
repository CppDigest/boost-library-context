# #998 Refactor string parsing [Merged]

> Username: grisumbras  
> Created at: 2024-03-29 22:50:18 UTC  
> Updated at: 2024-05-28 06:19:04 UTC  
> Merged at: 2024-05-28 06:18:42 UTC  
> Closed at: 2024-05-28 06:18:42 UTC  
> Url: https://github.com/boostorg/json/pull/998  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-30 00:06:59 UTC  
> Url: https://github.com/boostorg/json/pull/998#issuecomment-2027834552  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest-condensed-09cd7c6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-30 11:37:42 UTC  
> Updated_at: 2024-05-27 20:55:48 UTC  
> Url: https://github.com/boostorg/json/pull/998#issuecomment-2028022322  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/998?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.68212%` with `7 lines` in your changes are missing coverage. Please review.  
> Project coverage is 93.34%. Comparing base [(`c5c19cf`)](https://app.codecov.io/gh/boostorg/json/commit/c5c19cf4a1ade0625c2096132663446d93588560?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c4af9af`)](https://app.codecov.io/gh/boostorg/json/commit/c4af9affc0d87f87b0fee3fc6d66ec9360d28ae0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/998/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/998?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #998      +/-   ##  
===========================================  
- Coverage    93.41%   93.34%   -0.07%       
===========================================  
  Files           87       87                
  Lines         8547     8490      -57       
===========================================  
- Hits          7984     7925      -59       
- Misses         563      565       +2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/998?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/998?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/998?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.35% <97.68%> (-0.14%)` | :arrow_down: |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/json/pull/998/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/998?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/998?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c5c19cf...c4af9af](https://app.codecov.io/gh/boostorg/json/pull/998?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-03-30 13:06:57 UTC  
> Url: https://github.com/boostorg/json/pull/998#issuecomment-2028048807  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest-condensed-fbc26f7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-04-01 20:37:00 UTC  
> Url: https://github.com/boostorg/json/pull/998#issuecomment-2030513570  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest-condensed-c736f45.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-05-13 15:56:31 UTC  
> Url: https://github.com/boostorg/json/pull/998#issuecomment-2108090011  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest-condensed-0fbceda.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-05-27 21:32:04 UTC  
> Url: https://github.com/boostorg/json/pull/998#issuecomment-2134065468  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest-condensed-e433ca3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/998/pullrequest.html)

---
