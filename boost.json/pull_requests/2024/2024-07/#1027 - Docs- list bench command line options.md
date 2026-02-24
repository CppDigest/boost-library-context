# #1027 Docs: list bench command line options [Closed]

> Username: sdarwin  
> Created at: 2024-07-19 12:20:17 UTC  
> Updated at: 2024-07-25 20:56:07 UTC  
> Closed at: 2024-07-25 20:56:07 UTC  
> Url: https://github.com/boostorg/json/pull/1027  

It's not always convenient to "run the bench program with no arguments for a list of command line options" if you want to review the command line options. That requires building and compiling everything.   How about including options in the README page.  
  
One of those options is `-f   Include file IO into consideration when testing parsers`.  Could you double-check and confirm that ordinarily (without the -f option) the benchmarks avoid including file IO in the calculations?  Does that include both ingesting files prior to running the benchmark, as well as writing the results afterwards?  Both of those?  Therefore, when diagnosing the benchmark results the file system read/write time can be ignored?  A (variably) slow disk system would not affect the benchmarks?

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-19 12:27:22 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2239031359  

An automated preview of the documentation is available at [https://1027.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1027.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-07-19 12:54:16 UTC  
> Updated_at: 2024-07-24 13:51:12 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2239072386  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1027?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.40%. Comparing base [(`baff1cb`)](https://app.codecov.io/gh/boostorg/json/commit/baff1cb7ff99f0c5d8f741f9dbacdb76bf603877?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9a55ded`)](https://app.codecov.io/gh/boostorg/json/commit/9a55ded7299cf23f902c8c09e444dfbac0284efd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1027/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1027?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1027   +/-   ##  
========================================  
  Coverage    93.40%   93.40%             
========================================  
  Files           87       87             
  Lines         8622     8622             
========================================  
  Hits          8053     8053             
  Misses         569      569             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1027?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1027?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [baff1cb...9a55ded](https://app.codecov.io/gh/boostorg/json/pull/1027?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: grisumbras  
> Created_at: 2024-07-19 13:10:05 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2239103762  

`-f` uses a completely different function, so this mode should not affect the regular mode. In regular mode the benchmark reads the file, and then uses this information to parse/serialize the document without suspensions. The original read does not affect that calculation (in fact, the code written in a way, so it cannot affect it). With `-f` Boost.JSON implementations  use partial reads and partial parses. I introduced this mode in order to test two things:  
  
1. How comparable is it to use verbose approach of setting up a streaming parser, as opposed to just using iostreams.  
2. Whether it is better to read a file entirely into memory, then parse it without suspensions, or to parse available chunks.  
  
When counting the time taken by the functions in `-f` mode the time spent on opening, reading, closing the file, as well as on parsing it, is counted. For each iteration. Priniting the results is not counted. I haven't implemented this mode for serialization, as it opens the question of where to write the data to. This can be affected by slow FS, and disk caching. So, admittedly, it's not a very representative benchmark.  
  
Regular mode should not be affected by FS operations, as far as I can tell. If it is, it's definitely a bug.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-07-19 13:33:20 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2239193942  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest-condensed-f593025.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest.html)

---

## Comment 5

> Username: sdarwin  
> Created_at: 2024-07-19 14:05:37 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2239260678  

> Regular mode should not be affected by FS operations, as far as I can tell.  
  
Yes ok.  Since the existing benchmarks are stable it seems probable that filesystem operations aren't interfering.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-07-23 14:02:24 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2245347573  

An automated preview of the documentation is available at [https://1027.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1027.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-07-23 15:05:06 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2245506159  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest-condensed-f54fb48.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest.html)

---

## Comment 8

> Username: sdarwin  
> Created_at: 2024-07-23 15:48:50 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2245614390  

The new benchmark runner is an Intel(R) Xeon(R) E-2224 CPU @ 3.40GHz from knownhost.com.  First released in May 2019.  
  
The intel i5 from last week was consistently averaging 30-35 out of 56 benchmarks differing from a perfect measurement of 0% .  
  
The results in this pull request on the E-2224 shows 17 out 56 benchmarks differing from 0%, which is significantly better than the i5.    However, still worse than the earlier Intel(R) Xeon(R) CPU E5620  @ 2.40GHz.     It seems that less is more.  As Intel designs more power management features in their newer CPUs, that comes at a cost of fluctuations and variability.  
  
The E-2224 is now "in production" for boostorg/json, let's observe how the benchmarks go.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-07-24 13:17:27 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2247911219  

An automated preview of the documentation is available at [https://1027.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1027.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-07-24 15:18:49 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2248285529  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest-condensed-97bcc97.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1027/pullrequest.html)

---

## Comment 11

> Username: grisumbras  
> Created_at: 2024-07-25 20:37:00 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2251352413  

So, can I merge this?

---

## Comment 12

> Username: sdarwin  
> Created_at: 2024-07-25 20:49:19 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2251371225  

Yes

---

## Comment 13

> Username: grisumbras  
> Created_at: 2024-07-25 20:56:07 UTC  
> Url: https://github.com/boostorg/json/pull/1027#issuecomment-2251381190  

Merged via 56f3e1716345f5ceb2f03e78ba9f6b803990d034

---
