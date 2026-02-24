# #1088 enable conversion for derived described structs [Merged]

> Username: grisumbras  
> Created at: 2025-06-24 11:53:39 UTC  
> Updated at: 2025-06-26 20:33:35 UTC  
> Merged at: 2025-06-25 13:15:00 UTC  
> Closed at: 2025-06-25 13:15:00 UTC  
> Url: https://github.com/boostorg/json/pull/1088  

Fix #1086.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-06-24 11:59:51 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3000053229  

An automated preview of the documentation is available at [https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-06-24 12:08:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/1088#pullrequestreview-2953523027  

📁 include/boost/json/conversion.hpp

```diff
 297 |+     - `boost::describe::describe_bases<T,boost::describe::mod_private |
 298 |+       boost::describe::mod_protected | boost::describe::mod_inherited>` denotes
 299 |+       `L<>`; and
```

> Username: pdimov  
> Created_at: 2025-06-24 12:08:26 UTC  
> Url: https://github.com/boostorg/json/pull/1088#discussion_r2163792642  

This check is unnecessary. If describe_members with mod_inherited gives you public members only, we're good to go. There's no need to check the base classes. A private base that contains no data members is fine.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-06-24 13:05:12 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3000415815  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest-condensed-e4bfdab.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-06-24 14:04:53 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3000648999  

An automated preview of the documentation is available at [https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-06-24 15:10:02 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3000891415  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest-condensed-1caad61.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-06-24 15:59:52 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3001064273  

An automated preview of the documentation is available at [https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-06-24 17:05:05 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3001233661  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest-condensed-7abd824.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2025-06-25 09:59:53 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3004173741  

An automated preview of the documentation is available at [https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2025-06-25 10:52:05 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3004335192  

An automated preview of the documentation is available at [https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2025-06-25 11:05:06 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3004369019  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest-condensed-67b7727.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2025-06-25 11:14:47 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3004393763  

An automated preview of the documentation is available at [https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1088.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: codecov[bot]  
> Created_at: 2025-06-25 12:00:03 UTC  
> Updated_at: 2025-06-26 20:33:35 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3004512236  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1088?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.70%. Comparing base [(`6c79a2c`)](https://app.codecov.io/gh/boostorg/json/commit/6c79a2cf88eab40b519aabd944dfd83c5ba89c32?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`bfedf3c`)](https://app.codecov.io/gh/boostorg/json/commit/bfedf3ca5dd7414bb1e7251a69fbf9768e14a1ca?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1088/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1088?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1088   +/-   ##  
========================================  
  Coverage    93.70%   93.70%             
========================================  
  Files           91       91             
  Lines         9136     9136             
========================================  
  Hits          8561     8561             
  Misses         575      575             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1088?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/parse\_into.hpp](https://app.codecov.io/gh/boostorg/json/pull/1088?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fparse_into.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9wYXJzZV9pbnRvLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_from.hpp](https://app.codecov.io/gh/boostorg/json/pull/1088?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue_from.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/1088?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue_to.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/conversion.hpp](https://app.codecov.io/gh/boostorg/json/pull/1088?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fconversion.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/serializer.hpp](https://app.codecov.io/gh/boostorg/json/pull/1088?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fserializer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5ocHA=) | `96.40% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1088?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1088?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6c79a2c...bfedf3c](https://app.codecov.io/gh/boostorg/json/pull/1088?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2025-06-25 12:15:55 UTC  
> Url: https://github.com/boostorg/json/pull/1088#issuecomment-3004557775  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest-condensed-f3e1d3e.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1088/pullrequest.html)

---
