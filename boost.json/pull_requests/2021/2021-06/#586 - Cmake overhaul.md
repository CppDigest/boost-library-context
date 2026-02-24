# #586 Cmake overhaul [Merged]

> Username: grisumbras  
> Created at: 2021-06-22 15:58:23 UTC  
> Updated at: 2021-06-28 07:23:59 UTC  
> Merged at: 2021-06-28 07:14:05 UTC  
> Closed at: 2021-06-28 07:14:05 UTC  
> Url: https://github.com/boostorg/json/pull/586  

Some refactoring of root and test CMakeLists.txt   
  
* Add json tests to tests target (as has been requested by @pdimov in #581)   
* Refactor json usage requirements into a separate CMake target (fixes #562)  
* Overhaul of dealing with CMake use cases (fixes #582)  
* Fix test standalone_with_boost for CMake (fixes #577)  
* Other minor changes

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-06-22 16:44:48 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866153443  

This will need to be squashed down before merge

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-06-22 17:14:06 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866175407  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-0d6ea6e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-06-22 18:19:47 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866224605  

I'm not a fan of using fake targets, this pollutes installation.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-06-22 18:22:55 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866226567  

Which one is the fake target?

---

## Comment 5

> Username: grisumbras  
> Created_at: 2021-06-23 04:27:55 UTC  
> Updated_at: 2021-06-23 04:32:49 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866512396  

> Which one is the fake target?  
  
`json_private`. In actuality it is a header-only version of JSON, but Peter is probably correct that users that use build systems don't need a header-only target, so I called `private` to signify that it's not for users.  
  
 I had another Idea: setup properties via a function. E.g.  
```  
# CMakelists.txt  
function(json_setup_properties target)  
...  
endfunction()  
  
json_setup_properties(boost_json)  
  
# test/CMakelists.txt  
  
json_setup_properties(boost_json-limits)  
json_setup_properties(boost_json-standalone_with_boost)  
```  
  
This avoids the extra target but looks a bit less natural.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2021-06-23 05:51:34 UTC  
> Updated_at: 2021-06-28 07:13:45 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866548947  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/586?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#586](https://codecov.io/gh/boostorg/json/pull/586?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (67356cf) into [develop](https://codecov.io/gh/boostorg/json/commit/445efad9a97bf971986860088e17c3b0ff55bbac?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (445efad) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 67356cf differs from pull request most recent head 745be88. Consider uploading reports for the commit 745be88 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/586/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/586?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #586   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6114     6114             
========================================  
  Hits          6056     6056             
  Misses          58       58             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/586?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/586?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [445efad...745be88](https://codecov.io/gh/boostorg/json/pull/586?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-06-23 06:54:03 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866579258  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-9b02dc4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-06-23 12:04:02 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-866777512  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-412c56a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2021-06-23 19:39:06 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-867107079  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-0002aed.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-06-25 10:44:03 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-868410013  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-3c949cc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2021-06-26 18:39:03 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-869044763  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-2ddc695.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2021-06-26 20:23:57 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-869057544  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-5d8537c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2021-06-27 00:14:02 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-869077913  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-d36051e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2021-06-28 07:23:59 UTC  
> Url: https://github.com/boostorg/json/pull/586#issuecomment-869428356  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest-condensed-9253770.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/586/pullrequest.html)

---
