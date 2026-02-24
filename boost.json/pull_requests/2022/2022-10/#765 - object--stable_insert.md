# #765 object::stable_insert [Closed]

> Username: gummif  
> Created at: 2022-10-11 21:16:01 UTC  
> Updated at: 2026-01-26 13:24:12 UTC  
> Closed at: 2026-01-26 13:23:52 UTC  
> Url: https://github.com/boostorg/json/pull/765  

This is for inserting values at any position in the object preserving order of all elements (at the cost of linear complexity). Part 2/2 of https://github.com/boostorg/json/issues/748.  
  
This does not have the bug https://github.com/boostorg/json/issues/763 since care is taken to avoid allocation unless necessary.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-11 21:29:14 UTC  
> Updated_at: 2026-01-26 13:24:12 UTC  
> Url: https://github.com/boostorg/json/pull/765#issuecomment-1275294870  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/765?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.97%. Comparing base ([`ce144b5`](https://app.codecov.io/gh/boostorg/json/commit/ce144b57e4acf9f98fbce026e1c67c037bbd79a2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`d854374`](https://app.codecov.io/gh/boostorg/json/commit/d8543748b958bf369c497f17a75698e068a8f04d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 335 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/765/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/765?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #765      +/-   ##  
===========================================  
- Coverage    99.13%   98.97%   -0.17%       
===========================================  
  Files           69       69                
  Lines         6587     6623      +36       
===========================================  
+ Hits          6530     6555      +25       
- Misses          57       68      +11       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/765?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/765?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://app.codecov.io/gh/boostorg/json/pull/765?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/765?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/json/pull/765/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/765?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/765?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ce144b5...d854374](https://app.codecov.io/gh/boostorg/json/pull/765?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-11 22:29:42 UTC  
> Url: https://github.com/boostorg/json/pull/765#issuecomment-1275350488  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/765/pullrequest-condensed-e323f20.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/765/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/765/pullrequest.html)

---

## Comment 3

> Username: gummif  
> Created_at: 2022-10-12 20:11:56 UTC  
> Url: https://github.com/boostorg/json/pull/765#issuecomment-1276681090  

One thing that could reduce duplication is to implement insert in terms of stable_insert_impl using pos as end().

---

## Comment 4

> Username: gummif  
> Created_at: 2026-01-26 13:23:52 UTC  
> Url: https://github.com/boostorg/json/pull/765#issuecomment-3799589641  

Stale

---
