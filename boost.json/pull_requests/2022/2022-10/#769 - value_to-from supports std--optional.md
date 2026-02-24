# #769 value_to/from supports std::optional [Merged]

> Username: grisumbras  
> Created at: 2022-10-14 17:22:00 UTC  
> Updated at: 2022-10-17 14:24:18 UTC  
> Merged at: 2022-10-17 14:13:06 UTC  
> Closed at: 2022-10-17 14:13:06 UTC  
> Url: https://github.com/boostorg/json/pull/769  

Fix #677

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2022-10-14 17:25:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/769#pullrequestreview-1142759572  

📁 include/boost/json/detail/value_from.hpp

```diff
 190 |+     value& jv,
 191 |+     std::nullopt_t)
 192 |+ {
```

> Username: pdimov  
> Created_at: 2022-10-14 17:25:34 UTC  
> Updated_at: 2022-10-14 17:25:35 UTC  
> Url: https://github.com/boostorg/json/pull/769#discussion_r995979456  

Shouldn't `nullopt_t` be marked as `is_null_like` instead?

> Username: grisumbras  
> Created_at: 2022-10-14 18:12:51 UTC  
> Updated_at: 2022-10-14 18:12:52 UTC  
> Url: https://github.com/boostorg/json/pull/769#discussion_r996015148  

The generic implementation does not work, because std::nullopt_t is not default constructible.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2022-10-14 17:26:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/769#pullrequestreview-1142760638  

📁 include/boost/json/detail/value_from.hpp

```diff
 179 |+     using InnerRef = decltype( *static_cast<T>(from) );
 180 |+     if( from )
 181 |+         value_from(static_cast<InnerRef>(*from), jv);
```

> Username: pdimov  
> Created_at: 2022-10-14 17:26:33 UTC  
> Url: https://github.com/boostorg/json/pull/769#discussion_r995980221  

Why is this `static_cast` necessary? Why not just `*from`?


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-14 18:34:34 UTC  
> Url: https://github.com/boostorg/json/pull/769#issuecomment-1279335350  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/769/pullrequest-condensed-53d69b1.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/769/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/769/pullrequest.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-10-16 07:26:47 UTC  
> Updated_at: 2022-10-16 07:54:02 UTC  
> Url: https://github.com/boostorg/json/pull/769#issuecomment-1279911380  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/769?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#769](https://codecov.io/gh/boostorg/json/pull/769?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8033159) into [develop](https://codecov.io/gh/boostorg/json/commit/db3675d8c4b00b263de8079f6c8567c1bd79932b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (db3675d) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/769/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/769?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #769   +/-   ##  
========================================  
  Coverage    98.99%   98.99%             
========================================  
  Files           70       70             
  Lines         6734     6734             
========================================  
  Hits          6666     6666             
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/769?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/769/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/769/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/769/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/769/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/769?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/769?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [db3675d...8033159](https://codecov.io/gh/boostorg/json/pull/769?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-16 08:54:33 UTC  
> Url: https://github.com/boostorg/json/pull/769#issuecomment-1279924768  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/769/pullrequest-condensed-1a69087.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/769/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/769/pullrequest.html)

---
