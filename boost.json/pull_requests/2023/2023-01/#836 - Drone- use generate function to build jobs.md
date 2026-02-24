# #836 Drone: use generate function to build jobs [Open]

> Username: sdarwin  
> Created at: 2023-01-09 16:40:09 UTC  
> Updated at: 2023-01-11 21:34:48 UTC  
> Url: https://github.com/boostorg/json/pull/836  

Alan has abstracted the standard cpp.al drone jobs into a `generate` function that can be used on all repos, so adding a new compiler only needs to be done once in a central location.    
  
`generate` will probably be moved to the boost-ci repository eventually.  
  
Specific notes about json: gcc 4.8 and 4.9 were using Ubuntu 14.04 Trusty before. When switching those to Ubuntu 16.04 Xenial there are errors.  Skipping 4.8 and 4.9 now. You could test locally, or change .drone.star to 'gcc >=4.8' instead of 'gcc >=5.0'. Since Ubuntu 14.04 is nearly obsolete, that operating system should not continue to be used.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-01-09 16:54:24 UTC  
> Updated_at: 2023-01-11 20:20:19 UTC  
> Url: https://github.com/boostorg/json/pull/836#issuecomment-1375944093  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#836](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (48b75c7) into [develop](https://codecov.io/gh/boostorg/json/commit/607b21d827cef90233355be37054338435099074?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (607b21d) will **decrease** coverage by `0.04%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 48b75c7 differs from pull request most recent head 7d1fe09. Consider uploading reports for the commit 7d1fe09 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/836/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #836      +/-   ##  
===========================================  
- Coverage    99.00%   98.95%   -0.05%       
===========================================  
  Files           70       71       +1       
  Lines         6837     7088     +251       
===========================================  
+ Hits          6769     7014     +245       
- Misses          68       74       +6       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `87.50% <0.00%> (-12.50%)` | :arrow_down: |  
| [...nclude/boost/json/detail/impl/default\_resource.ipp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2RlZmF1bHRfcmVzb3VyY2UuaXBw) | `87.50% <0.00%> (-12.50%)` | :arrow_down: |  
| [include/boost/json/impl/parse.ipp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2UuaXBw) | `96.07% <0.00%> (-3.93%)` | :arrow_down: |  
| [include/boost/json/impl/value\_ref.ipp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfcmVmLmlwcA==) | `96.00% <0.00%> (-1.30%)` | :arrow_down: |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `98.87% <0.00%> (-1.13%)` | :arrow_down: |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `99.76% <0.00%> (-0.24%)` | :arrow_down: |  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value\_ref.hpp](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <0.00%> (ø)` | |  
| ... and [33 more](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [607b21d...7d1fe09](https://codecov.io/gh/boostorg/json/pull/836?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-01-09 17:54:47 UTC  
> Url: https://github.com/boostorg/json/pull/836#issuecomment-1376025136  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/836/pullrequest-condensed-349ff2c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/836/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/836/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-01-11 21:34:47 UTC  
> Url: https://github.com/boostorg/json/pull/836#issuecomment-1379513811  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/836/pullrequest-condensed-4c7a655.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/836/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/836/pullrequest.html)

---
