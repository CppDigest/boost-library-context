# #993 Use Boost.Charconv [Open]

> Username: grisumbras  
> Created at: 2024-03-19 12:25:16 UTC  
> Updated at: 2025-09-04 21:02:31 UTC  
> Url: https://github.com/boostorg/json/pull/993  

1. Removes embedded older version of Charconv and instead uses the library proper. Fix #1032.  
2. Removes embedded version of ryu and uses Charconv for double formatting.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-19 14:36:47 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2007355116  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-97de392.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-03-25 19:24:58 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2018747004  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-0f36bdc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-12-14 12:05:11 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2543077918  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-12-14 15:40:12 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2543159046  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-12-14 17:00:08 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2543184675  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-12-14 17:10:06 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2543187586  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-12-14 17:16:05 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2543190046  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2024-12-14 17:53:08 UTC  
> Updated_at: 2025-09-04 21:02:31 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2543206918  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/993?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.50%. Comparing base ([`2df2366`](https://app.codecov.io/gh/boostorg/json/commit/2df2366f74ad0ef4162855fb38dde155dbf42453?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`bcb9865`](https://app.codecov.io/gh/boostorg/json/commit/bcb98653cefa45d945bc260d6ac882fce495f18b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/993/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/993?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #993      +/-   ##  
===========================================  
- Coverage    93.71%   93.50%   -0.22%       
===========================================  
  Files           91       85       -6       
  Lines         9156     8840     -316       
===========================================  
- Hits          8581     8266     -315       
+ Misses         575      574       -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/993?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/format.ipp](https://app.codecov.io/gh/boostorg/json/pull/993?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fimpl%2Fformat.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2Zvcm1hdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/993?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/993?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2df2366...bcb9865](https://app.codecov.io/gh/boostorg/json/pull/993?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-12-15 01:53:40 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2543408046  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-ddfda74.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-12-16 09:20:10 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2545031067  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-12-16 10:22:46 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2545181636  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-f97238b.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2025-02-19 10:53:32 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668280527  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2025-02-19 11:13:24 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668328699  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2025-02-19 11:19:43 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668342910  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2025-02-19 11:26:00 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668357122  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2025-02-19 11:32:12 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668370881  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2025-02-19 11:58:07 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668438021  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-659b951.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2025-02-19 13:08:52 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668608395  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-c94daa0.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 19

> Username: sdarwin  
> Created_at: 2025-02-19 14:17:34 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668789316  

This is crashing the s390 runners. out-of-memory.  
The PR was opened 10 months ago.  Have you rebased it on `develop`?  
I now adjusted those runners from 2 jobs to 1 job.  Try it again.  
  
![Screenshot from 2025-02-19 07-13-23](https://github.com/user-attachments/assets/61ca4840-741d-4f96-af2c-a4ef067da23a)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2025-02-19 14:19:31 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668794738  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-464dc76.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2025-02-19 15:30:13 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2668993529  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-e7fa413.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2025-02-19 16:40:56 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2669184956  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-c785eb2.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 23

> Username: grisumbras  
> Created_at: 2025-02-19 17:08:55 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2669256438  

I did rebase it on develop.  
  
It is quite interesting that the build is crashing s390 runners. This might be a problem with Boost.Charconv.

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2025-02-19 18:13:26 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2669414392  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2025-02-19 19:18:03 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2669547355  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-15034cc.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 26

> Username: sdarwin  
> Created_at: 2025-02-19 19:23:56 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2669559128  

> This might be a problem with Boost.Charconv.  
  
It used more than 8GB of memory.  
  
I have just kicked off another run on 'develop', to compare that.

---

## Comment 27

> Username: sdarwin  
> Created_at: 2025-02-19 20:52:42 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2669734432  

Depending on the test, even the `develop` branch may get to nearly 8GB.     
Charconv is not the cause.     
I don't think JSON was using that much memory a couple years ago.     
In any case the runner is now parallel-jobs=1 so more memory is available.

---

## Comment 28

> Username: grisumbras  
> Created_at: 2025-02-21 11:44:46 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2674335540  

I've recorded memory usage when building and running tests. The outlier is pretty much compiling parse_into.cpp:  
| Config | Max mem usage |  
|---|---|  
| toolset=gcc variant=release cxxstd=20 | 3.22GB |  
| toolset=gcc variant=release cxxstd=17 | 3.15GB |  
| toolset=gcc variant=debug cxxstd=20 | 3.10GB |  
| toolset=gcc variant=debug cxxstd=17 | 2.95GB |  
| toolset=clang variant=debug cxxstd=20 | 2.02GB |  
| toolset=clang variant=debug cxxstd=17 | 2.00GB |  
| toolset=clang variant=release cxxstd=20 | 1.51GB |  
| toolset=clang variant=release cxxstd=17 | 1.42GB |  
  
Everthing else is below 0.9GB (https://gist.github.com/grisumbras/5985960c7781c4f657a93605d9e2b0ed).  
  
Unfortuantely, this is inherent to `parse_into.cpp`. It's very template-heavy. I think, the way we should deal with this is to move it to a separate build target, and run b2 twice: one for regular jobs with regular job count limit, and then the heavy tests one job at a time.

---

## Comment 29

> Username: grisumbras  
> Created_at: 2025-02-21 11:48:56 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-2674343818  

BTW, for posterity, the info was collected thusly:  
  
```  
# user-config.jam  
using gcc : 13 : /usr/bin/time "-f%C|%M" -o/home/grisumbras/dev/stats -a g++-13 ;  
using clang : 20 : /usr/bin/time "-f%C|%M" -o/home/grisumbras/dev/stats -a clang++-20 ;  
```  
  
And then b2 was invoked with  
```  
rm /home/grisumbras/dev/stats  
b2 test example toolset=gcc,clang variant=debug,release cxxstd=17,20 warnings=extra testing.launcher="/usr/bin/time -f'%C|%M' -o/home/grisumbras/dev/stats -a"  
```  
  
After that the file `/home/grisumbras/dev/stats` contained collected statistics.

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2025-08-04 20:15:14 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3152213566  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2025-08-04 21:17:03 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3152432527  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-7e470a7.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2025-08-06 08:25:07 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3158168514  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2025-08-06 09:26:57 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3158590132  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-0788608.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2025-08-14 11:50:10 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3188173517  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2025-08-14 12:51:59 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3188346886  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-5977ae5.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2025-08-25 11:15:04 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3219854029  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2025-08-25 12:16:58 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3220039908  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-0aec5e1.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2025-09-04 14:30:04 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3254015778  

An automated preview of the documentation is available at [https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://993.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2025-09-04 15:32:11 UTC  
> Url: https://github.com/boostorg/json/pull/993#issuecomment-3254255827  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest-condensed-38708bb.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/993/pullrequest.html)

---
