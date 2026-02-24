# #807 value_to supports missing elements for std::optional [Merged]

> Username: grisumbras  
> Created at: 2022-11-16 12:11:03 UTC  
> Updated at: 2023-01-02 13:12:51 UTC  
> Merged at: 2023-01-02 11:38:14 UTC  
> Closed at: 2023-01-02 11:38:14 UTC  
> Url: https://github.com/boostorg/json/pull/807  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-11-16 13:54:37 UTC  
> Url: https://github.com/boostorg/json/pull/807#issuecomment-1317060682  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest-condensed-9044c65.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-11-16 20:14:39 UTC  
> Url: https://github.com/boostorg/json/pull/807#issuecomment-1317611119  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest-condensed-29dc7f3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest.html)

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2022-11-25 12:39:07 UTC  
> Url: https://github.com/boostorg/json/pull/807#issuecomment-1327425483  

I could also use this with `boost::optional` - here's my use-case  
  
https://github.com/klemens-morgenstern/requests/blob/develop/example/github-issues.hpp#L94  
  
It's quite common in APIs, that `null` and non-existant are functionally the same.   
  
I guess one could use a `optional<variant<monostate, T>>` if those two cases have to be distinguished.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2022-11-26 09:38:00 UTC  
> Url: https://github.com/boostorg/json/pull/807#issuecomment-1328014635  

We probably need a special category for nullable types

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2023-01-02 11:26:44 UTC  
> Updated_at: 2023-01-02 11:28:00 UTC  
> Url: https://github.com/boostorg/json/pull/807#issuecomment-1368864104  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/807?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#807](https://codecov.io/gh/boostorg/json/pull/807?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8659e7d) into [develop](https://codecov.io/gh/boostorg/json/commit/a210032263b0550b0a0553beed90fdf72ba659d6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a210032) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/807/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/807?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #807   +/-   ##  
========================================  
  Coverage    99.01%   99.01%             
========================================  
  Files           70       70             
  Lines         6883     6883             
========================================  
  Hits          6815     6815             
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/807?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/807/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/807?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/807?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a210032...8659e7d](https://codecov.io/gh/boostorg/json/pull/807?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-01-02 13:12:51 UTC  
> Url: https://github.com/boostorg/json/pull/807#issuecomment-1368936003  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest-condensed-a9b53e4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/807/pullrequest.html)

---
