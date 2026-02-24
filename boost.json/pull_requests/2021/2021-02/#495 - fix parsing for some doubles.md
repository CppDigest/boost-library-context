# #495 fix parsing for some doubles [Closed]

> Username: grisumbras  
> Created at: 2021-02-01 18:55:50 UTC  
> Updated at: 2021-03-18 05:28:37 UTC  
> Closed at: 2021-03-01 13:08:01 UTC  
> Url: https://github.com/boostorg/json/pull/495  

Fixes #484

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-02-01 19:02:02 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-771083106  

Can we please not include whitespace changes? If you want to do this, do it in one big commit that does all the sources at once.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-02-01 19:20:08 UTC  
> Updated_at: 2021-02-02 06:25:03 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-771095262  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/495?src=pr&el=h1) Report  
> Merging [#495](https://codecov.io/gh/boostorg/json/pull/495?src=pr&el=desc) (bf847f7) into [develop](https://codecov.io/gh/boostorg/json/commit/f0ea69fefda1c8b02075814e32d86a74e216cc59?el=desc) (f0ea69f) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/495/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/495?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #495   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6047     6049    +2       
========================================  
+ Hits          5993     5995    +2       
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/495?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/495/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.19% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/495?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/495?src=pr&el=footer). Last update [f0ea69f...bf847f7](https://codecov.io/gh/boostorg/json/pull/495?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: grisumbras  
> Created_at: 2021-02-01 19:45:59 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-771111196  

Sorry, my editor is configured to remove trailing whitespace. I will remove ws changes.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-02-01 20:08:46 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-771124306  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/495/pullrequest-condensed-ecf7f81.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/495/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/495/pullrequest.html)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-02-01 22:55:13 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-771218336  

I don't mind the whitespace changes if they are separated into their own commit

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-02-02 07:13:44 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-771421583  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/495/pullrequest-condensed-56cf506.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/495/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/495/pullrequest.html)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-02-02 21:50:46 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-772020184  

This looks better, should we merge it?

---

## Comment 8

> Username: grisumbras  
> Created_at: 2021-02-03 05:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/495#issuecomment-772247789  

It looks like it negatively affects performance. I want to look into integrating Lemire's algorithm first.

---
