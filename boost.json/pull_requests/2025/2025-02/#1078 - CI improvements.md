# #1078 CI improvements [Merged]

> Username: grisumbras  
> Created at: 2025-02-27 12:24:07 UTC  
> Updated at: 2025-04-10 20:44:19 UTC  
> Merged at: 2025-03-05 09:37:53 UTC  
> Closed at: 2025-03-05 09:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/1078  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-02-27 12:33:13 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2687830811  

An automated preview of the documentation is available at [https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-02-27 13:14:00 UTC  
> Updated_at: 2025-04-10 20:44:19 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2687929818  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1078?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.70%. Comparing base [(`13d9527`)](https://app.codecov.io/gh/boostorg/json/commit/13d95270e92df03dad7c6838babbfac77d99b04c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0b1048a`)](https://app.codecov.io/gh/boostorg/json/commit/0b1048adf7571b42ae9f684b63e4383241b9e4a1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1078/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1078?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1078   +/-   ##  
========================================  
  Coverage    93.70%   93.70%             
========================================  
  Files           91       91             
  Lines         9139     9139             
========================================  
  Hits          8564     8564             
  Misses         575      575             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1078?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1078?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [13d9527...0b1048a](https://app.codecov.io/gh/boostorg/json/pull/1078?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-02-27 14:18:53 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688098824  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest-condensed-0b2fe71.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-02-27 14:33:33 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688140571  

An automated preview of the documentation is available at [https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: grisumbras  
> Created_at: 2025-02-27 14:42:38 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688165982  

@sdarwin can you switch s390 machines back to the normal amount of jobs? I changed CI script to launch the heavy job separately, which should avoid memory exhaustion.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2025-02-27 14:50:49 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688189313  

> launch the heavy job separately  
  
has it been merged?    
will that affect the 18 open pull requests still containing the earlier ci files? maybe not.  
It's safer to keep the settings for now.  At least jobs will run faster since they have the full machine CPU available.

---

## Comment 7

> Username: grisumbras  
> Created_at: 2025-02-27 15:02:12 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688222914  

Sure, but I can't check if this actually solves the problem, unless CI run it.

---

## Comment 8

> Username: sdarwin  
> Created_at: 2025-02-27 15:13:20 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688257297  

It's possible to observe the memory usage, this PR does help. Notice it doesn't peak at 16GB and crash.  
  
![Screenshot from 2025-02-27 08-11-25](https://github.com/user-attachments/assets/4fb670da-4d32-4348-a174-67bfad5ceb20)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2025-02-27 15:13:22 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688257388  

An automated preview of the documentation is available at [https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: grisumbras  
> Created_at: 2025-02-27 15:18:42 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688272870  

Lol, I see, thanks.

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2025-02-27 15:37:56 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688334445  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest-condensed-04f4cd2.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2025-02-27 16:48:38 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2688537638  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest-condensed-c3c3dcb.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2025-02-28 05:13:29 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2689743097  

An automated preview of the documentation is available at [https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1078.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2025-02-28 06:17:58 UTC  
> Url: https://github.com/boostorg/json/pull/1078#issuecomment-2689824973  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest-condensed-7575aca.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1078/pullrequest.html)

---
