# #892 accept numbers with large exponent [Merged]

> Username: grisumbras  
> Created at: 2023-05-19 06:12:55 UTC  
> Updated at: 2023-06-28 11:35:50 UTC  
> Merged at: 2023-06-28 11:00:34 UTC  
> Closed at: 2023-06-28 11:00:34 UTC  
> Url: https://github.com/boostorg/json/pull/892  

This changes treatment of numbers with exponents larger than `INT_MAX`. The current behaviour is somewhat arbitrary: we reject numbers with exponents that do not fit into `INT_MAX`, but we accept numbers with exponents that do not fit in `double` (we parse those numbers as infinity).  
  
This PR removes the inconsistency by just setting exponent to `INT_MAX`. The alternative solution to inconsistency -- making it an error if the exponent doesn't fit into `double` is undesirable. Parsing such numbers as infinity/zero is actually standard practice.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-19 06:29:53 UTC  
> Updated_at: 2023-06-28 10:50:59 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1554073954  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#892](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3d751ad) into [develop](https://app.codecov.io/gh/boostorg/json/commit/6a034f22d69ce229f7affcd17e5af982ff5e5e4a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6a034f2) will **decrease** coverage by `0.02%`.  
> The diff coverage is `88.23%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/892/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #892      +/-   ##  
===========================================  
- Coverage    92.99%   92.97%   -0.02%       
===========================================  
  Files           85       85                
  Lines         8061     8072      +11       
===========================================  
+ Hits          7496     7505       +9       
- Misses         565      567       +2       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.44% <88.23%> (-0.15%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6a034f2...3d751ad](https://app.codecov.io/gh/boostorg/json/pull/892?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-05-19 12:27:29 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1554499268  

This will theoretically fail for numbers like `0.000....0001e2147483648` where the number of zeroes is close to 2147483647, but I suppose this can never happen in practice.  
  
Does `TEST_GOOD` check that the parsed value is zero or infinity in these cases?

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-05-19 13:58:05 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1554616321  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest-condensed-f0131cf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html)

---

## Comment 4

> Username: grisumbras  
> Created_at: 2023-05-19 14:09:33 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1554644976  

> Does `TEST_GOOD` check that the parsed value is zero or infinity in these cases?  
  
No, it only tests that parse doesn't result in an error.

---

## Comment 5

> Username: grisumbras  
> Created_at: 2023-05-19 14:31:13 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1554681148  

> This will theoretically fail for numbers like `0.000....0001e2147483648` where the number of zeroes is close to 2147483647, but I suppose this can never happen in practice.  
  
Maybe we should consider such numbers errors instead (as far as I can see, we don't even check for overflow of the corresponding variable at the moment).

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-05-19 15:05:46 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1554727951  

Considering them errors should be fine.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-06-26 15:45:50 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1607751690  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest-condensed-a068f02.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-06-26 19:55:44 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1608142923  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest-condensed-54996b5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-06-26 21:55:43 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1608370099  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest-condensed-13497c8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-06-28 11:35:49 UTC  
> Url: https://github.com/boostorg/json/pull/892#issuecomment-1611241872  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest-condensed-a98eb8d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/892/pullrequest.html)

---
