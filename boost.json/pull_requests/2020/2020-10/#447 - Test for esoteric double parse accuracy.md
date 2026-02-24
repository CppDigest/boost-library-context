# #447 Test for esoteric double parse accuracy [Closed]

> Username: madmongo1  
> Created at: 2020-10-09 17:53:51 UTC  
> Updated at: 2020-10-21 19:43:46 UTC  
> Closed at: 2020-10-21 19:06:40 UTC  
> Url: https://github.com/boostorg/json/pull/447  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-10-12 01:15:36 UTC  
> Updated_at: 2020-10-21 18:29:57 UTC  
> Url: https://github.com/boostorg/json/pull/447#issuecomment-706804619  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/447?src=pr&el=h1) Report  
> Merging [#447](https://codecov.io/gh/boostorg/json/pull/447?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/json/commit/3f85abfe2946b28d1746ee378a0f35a2df571685?el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/447/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/447?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #447      +/-   ##  
===========================================  
- Coverage    99.10%   99.10%   -0.01%       
===========================================  
  Files           67       67                
  Lines         5950     5947       -3       
===========================================  
- Hits          5897     5894       -3       
  Misses          53       53                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/447?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/447/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/447?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/447?src=pr&el=footer). Last update [3f85abf...1c0636e](https://codecov.io/gh/boostorg/json/pull/447?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-10-14 02:07:28 UTC  
> Url: https://github.com/boostorg/json/pull/447#issuecomment-708109489  

This looks like it is failing on Azure?

---

## Comment 3

> Username: madmongo1  
> Created_at: 2020-10-14 05:58:22 UTC  
> Url: https://github.com/boostorg/json/pull/447#issuecomment-708175723  

Yep this is the issue I mentioned on slack. Vs2017 produces a different result to all other compilers by 2ulp for that case.  
Not sure where we go from here.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-10-16 20:04:14 UTC  
> Url: https://github.com/boostorg/json/pull/447#issuecomment-710532193  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/447/pullrequest-condensed-e311127.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/447/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/447/pullrequest.html)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-10-18 16:00:16 UTC  
> Url: https://github.com/boostorg/json/pull/447#issuecomment-711253229  

Can we increase the tolerance just for VS2017 and earlier?

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2020-10-21 19:43:46 UTC  
> Url: https://github.com/boostorg/json/pull/447#issuecomment-713832037  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/447/pullrequest-condensed-4c21113.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/447/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/447/pullrequest.html)

---
