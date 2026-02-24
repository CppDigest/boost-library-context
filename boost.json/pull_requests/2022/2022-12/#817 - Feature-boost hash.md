# #817 Feature/boost hash [Merged]

> Username: grisumbras  
> Created at: 2022-12-01 13:39:17 UTC  
> Updated at: 2023-01-02 10:49:41 UTC  
> Merged at: 2023-01-02 09:57:49 UTC  
> Closed at: 2023-01-02 09:57:49 UTC  
> Url: https://github.com/boostorg/json/pull/817  

Fix #570

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-12-01 13:56:22 UTC  
> Updated_at: 2023-01-02 09:55:43 UTC  
> Url: https://github.com/boostorg/json/pull/817#issuecomment-1333804567  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/817?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#817](https://codecov.io/gh/boostorg/json/pull/817?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c650832) into [develop](https://codecov.io/gh/boostorg/json/commit/0f987f07d7c343250714a42805c487dccaf4b1c0?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0f987f0) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/817/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #817      +/-   ##  
===========================================  
- Coverage    99.00%   99.00%   -0.01%       
===========================================  
  Files           71       70       -1       
  Lines         6832     6802      -30       
===========================================  
- Hits          6764     6734      -30       
  Misses          68       68                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value.hpp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/string.ipp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RyaW5nLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value.ipp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/817/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.95% <100.00%> (+0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/817?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/817?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0f987f0...c650832](https://codecov.io/gh/boostorg/json/pull/817?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-01 15:39:36 UTC  
> Url: https://github.com/boostorg/json/pull/817#issuecomment-1333955121  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest-condensed-9ed2232.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-12-01 20:39:34 UTC  
> Url: https://github.com/boostorg/json/pull/817#issuecomment-1334418619  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest-condensed-6e58c6e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-12-01 23:02:30 UTC  
> Url: https://github.com/boostorg/json/pull/817#issuecomment-1334555693  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest-condensed-819b5ca.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-12-02 12:49:39 UTC  
> Url: https://github.com/boostorg/json/pull/817#issuecomment-1335181881  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest-condensed-e4029f5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-01-02 10:49:41 UTC  
> Url: https://github.com/boostorg/json/pull/817#issuecomment-1368831741  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest-condensed-e917210.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/817/pullrequest.html)

---
