# #1143 Pass stable references to visitors [Open]

> Username: grisumbras  
> Created at: 2026-02-10 19:10:49 UTC  
> Updated at: 2026-02-23 10:32:06 UTC  
> Url: https://github.com/boostorg/json/pull/1143  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-02-10 19:21:04 UTC  
> Updated_at: 2026-02-20 14:50:47 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3880216991  

An automated preview of the documentation is available at [https://1143.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1143.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-02-20 14:50:46 UTC

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2026-02-10 19:51:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/1143#pullrequestreview-3781210406  

📁 include/boost/json/impl/visit.hpp

```diff
  67 |         static_cast<Visitor&&>(v)( std::declval<std::nullptr_t&&>() ) )
  68 | {
  69 |+     auto const& scalar = detail::access::get_scalar(jv);
```

> Username: pdimov  
> Created_at: 2026-02-10 19:51:18 UTC  
> Url: https://github.com/boostorg/json/pull/1143#discussion_r2789937512  

should this be `auto const&`? `jv` is a non-const rvalue reference.

> Username: grisumbras  
> Created_at: 2026-02-11 05:43:07 UTC  
> Url: https://github.com/boostorg/json/pull/1143#discussion_r2791508868  

Oops. Copy-paste error. I should change mys tests, since they didn't catch this.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2026-02-10 20:23:02 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3880512024  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest-condensed-11fa18f.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2026-02-12 12:29:51 UTC  
> Updated_at: 2026-02-20 15:20:09 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3890676487  

GCOVR code coverage report [https://1143.json.prtest2.cppalliance.org/gcovr/index.html](https://1143.json.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://1143.json.prtest2.cppalliance.org/genhtml/index.html](https://1143.json.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report [https://1143.json.prtest2.cppalliance.org/diff-report/index.html](https://1143.json.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-02-20 15:20:08 UTC

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2026-02-12 13:08:00 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3890861569  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest-condensed-188578d.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2026-02-13 16:58:03 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3898253717  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest-condensed-6ff2dc5.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2026-02-13 20:21:07 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3899267201  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest-condensed-b03afb2.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html)

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2026-02-18 17:22:30 UTC  
> Updated_at: 2026-02-19 19:14:03 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3922120384  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1143?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.67%. Comparing base ([`4df9a86`](https://app.codecov.io/gh/boostorg/json/commit/4df9a86a206beac4ac12ec8ac16a1561e83acacf?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`7d70fb3`](https://app.codecov.io/gh/boostorg/json/commit/7d70fb3210cece052a9cd8362440a44229d01dc1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1143/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1143?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1143   +/-   ##  
========================================  
  Coverage    93.67%   93.67%             
========================================  
  Files           91       91             
  Lines         9154     9161    +7       
========================================  
+ Hits          8575     8582    +7       
  Misses         579      579             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1143?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/1143?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value.ipp](https://app.codecov.io/gh/boostorg/json/pull/1143?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fvalue.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `99.12% <100.00%> (ø)` | |  
| [include/boost/json/impl/visit.hpp](https://app.codecov.io/gh/boostorg/json/pull/1143?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fvisit.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmlzaXQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/1143?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fvalue.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.83% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1143?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1143?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4df9a86...7d70fb3](https://app.codecov.io/gh/boostorg/json/pull/1143?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2026-02-18 17:55:37 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3922304283  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest-condensed-0ffc51a.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2026-02-19 18:53:01 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3929244690  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest-condensed-e1fcab2.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2026-02-20 15:52:56 UTC  
> Url: https://github.com/boostorg/json/pull/1143#issuecomment-3935710854  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest-condensed-2272f4e.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1143/pullrequest.html)

---
