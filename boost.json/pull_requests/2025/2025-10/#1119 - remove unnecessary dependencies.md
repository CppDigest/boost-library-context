# #1119 remove unnecessary dependencies [Merged]

> Username: grisumbras  
> Created at: 2025-10-22 10:49:25 UTC  
> Updated at: 2025-10-27 18:14:55 UTC  
> Merged at: 2025-10-27 11:51:29 UTC  
> Closed at: 2025-10-27 11:51:29 UTC  
> Url: https://github.com/boostorg/json/pull/1119  

Fix #1118  
Fix #1117

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-10-22 10:55:49 UTC  
> Updated_at: 2025-10-22 16:47:03 UTC  
> Url: https://github.com/boostorg/json/pull/1119#issuecomment-3431780248  

An automated preview of the documentation is available at [https://1119.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1119.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-10-22 16:47:01 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-10-22 16:28:49 UTC  
> Url: https://github.com/boostorg/json/pull/1119#issuecomment-3433240299  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest-condensed-6e5856c.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-10-22 17:48:50 UTC  
> Url: https://github.com/boostorg/json/pull/1119#issuecomment-3433513868  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest-condensed-fa2a4cf.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-10-22 18:23:03 UTC  
> Updated_at: 2025-10-27 18:14:55 UTC  
> Url: https://github.com/boostorg/json/pull/1119#issuecomment-3433660331  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1119?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.71%. Comparing base ([`a81a43d`](https://app.codecov.io/gh/boostorg/json/commit/a81a43da656c44a2456ca71415dc05ec0d5e3898?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`427baa6`](https://app.codecov.io/gh/boostorg/json/commit/427baa6e475e457a1a585e33957e2b0667fbe23e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1119/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1119      +/-   ##  
===========================================  
- Coverage    93.71%   93.71%   -0.01%       
===========================================  
  Files           91       91                
  Lines         9156     9153       -3       
===========================================  
- Hits          8581     8578       -3       
  Misses         575      575                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1119?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/config.hpp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fconfig.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/impl/stack.hpp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fimpl%2Fstack.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/sbo\_buffer.hpp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fsbo_buffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zYm9fYnVmZmVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/string\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fstring_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJpbmdfaW1wbC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue_to.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/array.hpp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Farray.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Farray.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/monotonic\_resource.ipp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fmonotonic_resource.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvbW9ub3RvbmljX3Jlc291cmNlLmlwcA==) | `97.01% <100.00%> (-0.09%)` | :arrow_down: |  
| [include/boost/json/impl/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <ø> (ø)` | |  
| ... and [8 more](https://app.codecov.io/gh/boostorg/json/pull/1119?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1119?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1119?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a81a43d...427baa6](https://app.codecov.io/gh/boostorg/json/pull/1119?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-10-24 16:07:00 UTC  
> Updated_at: 2025-10-24 16:41:56 UTC  
> Url: https://github.com/boostorg/json/pull/1119#issuecomment-3443910140  

An automated preview of the documentation is available at [https://1119.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1119.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-10-24 16:41:55 UTC

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-10-24 17:43:49 UTC  
> Url: https://github.com/boostorg/json/pull/1119#issuecomment-3444235676  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest-condensed-7f4a7d9.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1119/pullrequest.html)

---
