# #477 Add "cxxstd" json field. The "cxxstd" json field is being added to ea… [Closed]

> Username: eldiener  
> Created at: 2020-12-16 07:04:04 UTC  
> Updated at: 2020-12-17 18:14:19 UTC  
> Closed at: 2020-12-17 18:14:19 UTC  
> Url: https://github.com/boostorg/json/pull/477  

…ch Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-12-16 07:17:02 UTC  
> Updated_at: 2020-12-16 07:21:39 UTC  
> Url: https://github.com/boostorg/json/pull/477#issuecomment-745816459  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/477?src=pr&el=h1) Report  
> Merging [#477](https://codecov.io/gh/boostorg/json/pull/477?src=pr&el=desc) (5444565) into [develop](https://codecov.io/gh/boostorg/json/commit/edbf86641b7217676eceb6582ee486e31bb42745?el=desc) (edbf866) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/477/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/477?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #477   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6047     6047             
========================================  
  Hits          5993     5993             
  Misses          54       54             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/477?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/477?src=pr&el=footer). Last update [edbf866...5444565](https://codecov.io/gh/boostorg/json/pull/477?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-12-16 08:18:44 UTC  
> Url: https://github.com/boostorg/json/pull/477#issuecomment-745894390  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/477/pullrequest-condensed-3e2d68c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/477/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/477/pullrequest.html)

---
