# #890 Precise mode for parsing numbers [Merged]

> Username: grisumbras  
> Created at: 2023-05-17 19:34:46 UTC  
> Updated at: 2023-05-22 16:10:38 UTC  
> Merged at: 2023-05-22 15:40:36 UTC  
> Closed at: 2023-05-22 15:40:36 UTC  
> Url: https://github.com/boostorg/json/pull/890  

This is technically a fix to #599, but we want a better implementation than `strtod` (because it is very slow). Closer to the release I'll take some files from Charconv.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-05-17 22:45:26 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1552179692  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-c4bc32b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2023-05-18 01:09:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/890#pullrequestreview-1431869838  

📁 include/fast_float/parse_number.h

```diff
   1 |+ #ifndef FASTFLOAT_PARSE_NUMBER_H
   2 |+ #define FASTFLOAT_PARSE_NUMBER_H
   3 |+ 
```

> Username: vinniefalco  
> Created_at: 2023-05-18 01:09:17 UTC  
> Url: https://github.com/boostorg/json/pull/890#discussion_r1197233644  

You need copyrights and please "hpp" extension not "h"


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2023-05-18 01:10:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/890#pullrequestreview-1431870292  

📁 include/fast_float/parse_number.h

```diff
   4 |+ #include "ascii_number.h"
   5 |+ #include "decimal_to_binary.h"
   6 |+ #include "digit_comparison.h"
```

> Username: vinniefalco  
> Created_at: 2023-05-18 01:10:05 UTC  
> Url: https://github.com/boostorg/json/pull/890#discussion_r1197234191  

These need to be standard full boost paths, e.g. <boost/json/ umm... wait a minute you have this in <boost/fastfloat/> ? That's not allowed

> Username: grisumbras  
> Created_at: 2023-05-20 06:45:54 UTC  
> Updated_at: 2023-05-20 06:45:55 UTC  
> Url: https://github.com/boostorg/json/pull/890#discussion_r1199569259  

Turns out fast_float isn't permissive enough, so I had to remove it anyway.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2023-05-18 01:10:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/890#pullrequestreview-1431870393  

📁 include/fast_float/parse_number.h

```diff
  12 |+ #include <system_error>
  13 |+ 
  14 |+ namespace fast_float {
```

> Username: vinniefalco  
> Created_at: 2023-05-18 01:10:15 UTC  
> Updated_at: 2023-05-18 01:10:16 UTC  
> Url: https://github.com/boostorg/json/pull/890#discussion_r1197234298  

Where's the boost namespace?


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-05-18 07:30:55 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1552642401  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-715a0b4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-05-20 08:00:35 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1555837117  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-6337787.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2023-05-20 12:41:14 UTC  
> Updated_at: 2023-05-22 15:29:12 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1555903558  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#890](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (642a9ee) into [develop](https://app.codecov.io/gh/boostorg/json/commit/a99292b9ea8be81a9e4e02c4a6a3e9641dfad0e7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a99292b) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/890/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #890   +/-   ##  
========================================  
  Coverage    99.24%   99.25%             
========================================  
  Files           70       71    +1       
  Lines         6764     6844   +80       
========================================  
+ Hits          6713     6793   +80       
  Misses          51       51             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.59% <100.00%> (+0.04%)` | :arrow_up: |  
| [include/boost/json/detail/sbo\_buffer.hpp](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zYm9fYnVmZmVyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a99292b...642a9ee](https://app.codecov.io/gh/boostorg/json/pull/890?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-05-20 13:35:37 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1555913387  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-24720c4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-05-21 15:15:39 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1556204635  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-b3f9e1a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-05-22 08:20:38 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1556760815  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-bf1bd2c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-05-22 13:36:41 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1557236678  

An automated preview of the documentation is available at [https://890.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://890.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-05-22 14:45:38 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1557351432  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-5bb2c2b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-05-22 15:06:44 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1557389769  

An automated preview of the documentation is available at [https://890.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://890.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-05-22 16:10:38 UTC  
> Url: https://github.com/boostorg/json/pull/890#issuecomment-1557511976  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest-condensed-1e93ff6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/890/pullrequest.html)

---
