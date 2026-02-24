# #187 Feature/foa fast iteration [Merged]

> Username: joaquintides  
> Created at: 2023-03-07 18:59:43 UTC  
> Updated at: 2024-08-17 16:49:24 UTC  
> Merged at: 2023-03-20 19:43:30 UTC  
> Closed at: 2023-03-20 19:43:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/187  

Changed iterator increment code for "regular layout" group implementations (basically, those for SSE2 and Neon). Iteration speed improves dramatically, look for "Running erasure" in:  
  
https://github.com/boostorg/boost_unordered_benchmarks/commit/46129697b05fab67c42cfa2b918a6e40af26d6e6

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-07 19:06:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1458681338  

An automated preview of the documentation is available at [https://187.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://187.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-03-07 19:06:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1458681612  

Looks good to me, although I'm not sure we should be merging to develop now. Maybe wait for the beta to go out in case there are critical fixes we need to apply.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2023-03-07 19:07:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1458683207  

The release notes added in the PR target 1.83, I just submitted it because it's ready but we can wait of course.

---

## Review 4 [Commented]

> Username: cmazakas  
> Created_at: 2023-03-07 20:16:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/187#pullrequestreview-1329459882  

📁 include/boost/unordered/detail/foa.hpp

```diff
 875 |-   inline void increment()noexcept
 898 |+   inline void increment(std::true_type /* regular layout */)noexcept
 899 |+   {
```

> Username: cmazakas  
> Created_at: 2023-03-07 20:16:47 UTC  
> Updated_at: 2023-03-07 20:17:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#discussion_r1128535356  

Should we add a `BOOST_ASSERT(p);` or `BOOST_UNORDERED_ASSUME(p != nullptr)` here?


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-03-08 17:41:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1460567615  

An automated preview of the documentation is available at [https://187.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://187.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-03-08 18:21:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1460647244  

An automated preview of the documentation is available at [https://187.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://187.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2023-03-09 07:04:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1461438150  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#187](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1c5640c) into [develop](https://codecov.io/gh/boostorg/unordered/commit/9aedb95296acff88e377d6e06d317b5538980ec1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9aedb95) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/187/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #187   +/-   ##  
========================================  
  Coverage    97.85%   97.85%             
========================================  
  Files           91       91             
  Lines        13849    13875   +26       
========================================  
+ Hits         13552    13578   +26       
  Misses         297      297             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `99.84% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9aedb95...1c5640c](https://codecov.io/gh/boostorg/unordered/pull/187?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: joaquintides  
> Created_at: 2023-03-09 14:07:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1462127266  

Latest commit (1c5640cfbe84213b9d2a7eff68e2db744cfa3817) didn't affect performance:  
https://github.com/boostorg/boost_unordered_benchmarks/commit/90e178376ffefe73d3e6fc7fa24ef70a26d429a6

---

## Comment 9

> Username: cmazakas  
> Created_at: 2023-03-09 16:37:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1462380752  

> Latest commit ([1c5640c](https://github.com/boostorg/unordered/commit/1c5640cfbe84213b9d2a7eff68e2db744cfa3817)) didn't affect performance: [boostorg/boost_unordered_benchmarks@90e1783](https://github.com/boostorg/boost_unordered_benchmarks/commit/90e178376ffefe73d3e6fc7fa24ef70a26d429a6)  
  
Well, my logic was more about the code self-documenting the assumptions.  
  
It wasn't clear to me why we needed the null pointer check before calling `++it`.

---

## Comment 10

> Username: cmazakas  
> Created_at: 2023-03-20 15:46:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1476481112  

@pdimov @joaquintides   
  
Now that the Boost beta is out, should we feel free to go and merge this PR?

---

## Comment 11

> Username: joaquintides  
> Created_at: 2023-03-20 16:49:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1476590972  

FWIW, this feature is already integrated in [feature/cfoa](https://github.com/boostorg/unordered/tree/feature/cfoa), so we can either drop this PR or merge and rebase that branch.

---

## Comment 12

> Username: pdimov  
> Created_at: 2023-03-20 17:31:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/187#issuecomment-1476656170  

We can merge it to develop, I suppose, just not to master.

---
