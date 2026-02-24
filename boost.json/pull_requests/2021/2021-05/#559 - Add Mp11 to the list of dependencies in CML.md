# #559 Add Mp11 to the list of dependencies in CML [Merged]

> Username: grisumbras  
> Created at: 2021-05-03 20:04:05 UTC  
> Updated at: 2021-05-06 15:58:12 UTC  
> Merged at: 2021-05-06 15:57:50 UTC  
> Closed at: 2021-05-06 15:57:50 UTC  
> Url: https://github.com/boostorg/json/pull/559  

Fix #555   
  
Also, add tests for CMake build inside superproject (which relies on that dependency). Fix #558

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-05-03 20:05:39 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-831501015  

Commit message should read "depend on mp11"

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-05-03 20:19:00 UTC  
> Updated_at: 2021-05-04 14:14:22 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-831508414  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/559?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#559](https://codecov.io/gh/boostorg/json/pull/559?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bcd8ada) into [develop](https://codecov.io/gh/boostorg/json/commit/5e69b5a61e58140ea66583e8229e6b1938eee034?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5e69b5a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head bcd8ada differs from pull request most recent head 7c482b5. Consider uploading reports for the commit 7c482b5 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/559/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/559?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #559   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6058     6058             
========================================  
  Hits          6004     6004             
  Misses          54       54             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/559?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/559?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5e69b5a...7c482b5](https://codecov.io/gh/boostorg/json/pull/559?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-05-03 20:27:50 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-831513166  

Great

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-05-03 20:40:34 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-831520124  

@grisumbras   
  
```  
chmod 755 .drone/cmake-superproject-script.sh  
```

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2021-05-03 22:19:28 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-831571194  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest-condensed-c753498.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-05-04 12:03:55 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-831889286  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest-condensed-6c5d4cb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest.html)

---

## Comment 7

> Username: grisumbras  
> Created_at: 2021-05-04 13:41:21 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-831950947  

Do we need any other CMake superproject tests? @pdimov @vinniefalco

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-05-04 17:58:24 UTC  
> Url: https://github.com/boostorg/json/pull/559#issuecomment-832133030  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest-condensed-5bb86a6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/559/pullrequest.html)

---
