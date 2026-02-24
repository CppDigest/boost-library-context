# #1028 bench runner can run external command [Open]

> Username: grisumbras  
> Created at: 2024-07-19 12:37:52 UTC  
> Updated at: 2025-12-23 17:41:07 UTC  
> Url: https://github.com/boostorg/json/pull/1028  

Fix #1026.  
  
The idea is that you can run an external command from the benchmark. The command is communicated via an environment variable. E.g. in bash:  
  
```bash  
BOOST_JSON_BENCH_EXTERNAL_COMMAND="./1.sh" ./bench -i:b  
```  
  
The bench runner invokes the command with these arguments:  
1. `start` or `finish`  
2. toolset name (e.g. `gcc`, `clang`)  
3. the name of the processed file.  
  
For example, here's a script that does what was suggested in the issue:  
```sh  
#!/bin/sh  
  
op=Staring  
if [ "$1" = finish ]; then  
    op=Finished  
fi  
shift  
  
compiler=$1  
shift  
  
date +'%Y/%m/%d %H:%M:%S:%3N' >> /tmp/json-benchmarks.log  
echo "$op to process $* with $compiler" >> /tmp/json-benchmarks.log  
lscpu -e >> /tmp/json-benchmarks.log  
top -b -n 1 | head -n 20 >> /tmp/json-benchmarks.log  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-19 12:47:23 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239061960  

An automated preview of the documentation is available at [https://1028.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1028.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: sdarwin  
> Created_at: 2024-07-19 13:03:08 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239086854  

Hi @grisumbras , thanks!  
  
There were two ideas in the issue with the newer, later idea being a log file.     Upon further consideration I think that would have been preferable, having output nearly identical to the benchmark output, which makes correlating the logs and the benches as easy as possible.  
  
Well, in this case, I will need to generate the log file.   
  
By designing a "./1.sh".      
  
Can 1.sh generate this output, distinguishing Serialize and Parse, and including the bench results times?  
  
```  
2024-07-18-08:10:32:725 Starting Parse mesh.json,clang x64/sse2,boost (pool)  
2024-07-18-08:10:33:822 Completed Parse mesh.json,clang x64/sse2,boost (pool),3200,5006,441  
2024-07-18-08:10:34:321 Starting Parse mesh.json,clang x64/sse2,boost (pool)  
2024-07-18-08:10:35:425 Completed Parse mesh.json,clang x64/sse2,boost (pool),3339,5225,441  
```  
  
The bench code is/was already writing "Parse mesh.json,clang x64/sse2,boost (pool),3200,5006,441" .   Since it has that information,  is it available as a string, and passed to "./1.sh"?

---

## Comment 3

> Username: grisumbras  
> Created_at: 2024-07-19 13:11:30 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239110801  

Yeah, I think it should be possible.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-07-19 13:12:10 UTC  
> Updated_at: 2025-01-02 23:17:49 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239114841  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1028?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.40%. Comparing base [(`baff1cb`)](https://app.codecov.io/gh/boostorg/json/commit/baff1cb7ff99f0c5d8f741f9dbacdb76bf603877?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`365b46e`)](https://app.codecov.io/gh/boostorg/json/commit/365b46eee3e792ecb5c975b60d4fc6d86383097a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 56 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1028/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1028?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1028   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1028?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1028?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [baff1cb...365b46e](https://app.codecov.io/gh/boostorg/json/pull/1028?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: sdarwin  
> Created_at: 2024-07-19 13:19:49 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239161704  

The debugging commands `lscpu` and `top` ought to be run every second, constantly, during the benchmark tests.   Therefore, it becomes less critical to run them before and after each bench.   They must also run during the bench.   The way to implement that is to have them totally outside of `bench`.   (...I have composed another bash loop script).  
  
for the moment, the current most interesting goal is to generate a log file that looks as similar to the bench output as possible. (but adds timestamps. start/stop.)

---

## Comment 6

> Username: grisumbras  
> Created_at: 2024-07-19 13:57:24 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239243719  

With the new version the accomapying script would be  
```bash  
#!/bin/sh  
echo >>/tmp/json-benchmarks.log $(date +'%Y/%m/%d %H:%M:%S:%3N') "$@"  
```

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-07-19 14:02:23 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239254111  

An automated preview of the documentation is available at [https://1028.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1028.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: sdarwin  
> Created_at: 2024-07-19 14:06:12 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239262238  

Great!! I will test it.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-07-19 14:12:58 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239276387  

An automated preview of the documentation is available at [https://1028.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1028.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: sdarwin  
> Created_at: 2024-07-19 15:43:01 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239486134  

/tmp/benchmark_external_script.sh   
  
```  
#!/bin/bash  
  
touch external_script_is_working.txt  
touch /tmp/external_script_is_working.txt  
  
echo >>/tmp/json-benchmarks.log $(date +'%Y/%m/%d %H:%M:%S:%3N') "$@"  
```  
  
Bench command:    
  
BOOST_JSON_BENCH_EXTERNAL_COMMAND=/tmp/benchmark_external_script.sh ./bench -i:b *  
  
Quotes are missing around /tmp/benchmark_external_script.sh to avoid escaping quotes through multiple scripts. However that  shouldn't affect things.    
  
Result:  
  
The files `external_script_is_working.txt` and `json-benchmarks.log` do not appear anywhere in the filesystem.    
  
Locally, could you try to replicate this using `/tmp/benchmark_external_script.sh` (not ./benchmark_external_script.sh) and see what happens?

---

## Comment 11

> Username: grisumbras  
> Created_at: 2024-07-19 16:07:28 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239523534  

Everything works locally for me. Please check if the script has executable flag set.

---

## Comment 12

> Username: sdarwin  
> Created_at: 2024-07-19 16:14:02 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239533178  

Ok thanks.   I think I know what it is.

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-07-19 16:23:39 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239547058  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1028/pullrequest-condensed-a2a6d7b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1028/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1028/pullrequest.html)

---

## Comment 14

> Username: grisumbras  
> Created_at: 2024-07-19 16:32:52 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239576078  

Suddenly slow canada.json strikes again!

---

## Comment 15

> Username: sdarwin  
> Created_at: 2024-07-19 18:47:18 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2239909607  

Compare the numerical results at the end of the line.  
  
```  
Parse apache_builds.json,clang x64/sse2,boost (pool),15000,5039,361  
Parse apache_builds.json,clang x64/sse2,boost (pool),15624,5221,363  
Parse apache_builds.json,clang x64/sse2,boost (pool),15708,5245,364  
Parse apache_builds.json,clang x64/sse2,boost (pool),14960,5020,362  
Parse apache_builds.json,clang x64/sse2,boost (pool),15645,5237,363  
Parse apache_builds.json,clang x64/sse2,boost (pool),15666,5247,362  
```  
  
/tmp/json-benchmarks.log  
  
```  
2024/07/19 16:56:59:225 Starting Parse apache_builds.json,clang x64/sse2,boost (pool)  
2024/07/19 16:57:04:271 Completed Parse apache_builds.json,clang x64/sse2,boost (pool),15,5039,0  
2024/07/19 16:57:04:276 Starting Parse apache_builds.json,clang x64/sse2,boost (pool)  
2024/07/19 16:57:09:503 Completed Parse apache_builds.json,clang x64/sse2,boost (pool),21,5221,0  
2024/07/19 16:57:09:508 Starting Parse apache_builds.json,clang x64/sse2,boost (pool)  
2024/07/19 16:57:14:760 Completed Parse apache_builds.json,clang x64/sse2,boost (pool),21,5245,0  
2024/07/19 16:57:14:765 Starting Parse apache_builds.json,clang x64/sse2,boost (pool)  
2024/07/19 16:57:19:791 Completed Parse apache_builds.json,clang x64/sse2,boost (pool),20,5020,0  
2024/07/19 16:57:19:796 Starting Parse apache_builds.json,clang x64/sse2,boost (pool)  
2024/07/19 16:57:25:039 Completed Parse apache_builds.json,clang x64/sse2,boost (pool),21,5237,0  
2024/07/19 16:57:25:044 Starting Parse apache_builds.json,clang x64/sse2,boost (pool)  
2024/07/19 16:57:30:297 Completed Parse apache_builds.json,clang x64/sse2,boost (pool),21,5247,0  
```  
  
The current benchmarks might be lucky to have a chipset from 2010.   The new machine exhibits more randomness.  This is the problem -    
  
https://www.kernel.org/doc/Documentation/cpu-freq/intel-pstate.txt  
  
```  
For contemporary Intel processors, the frequency is controlled by the  
processor itself and the P-State exposed to software is related to  
performance levels.  The idea that frequency can be set to a single  
frequency is fictional for Intel Core processors. Even if the scaling  
driver selects a single P-State, the actual frequency the processor  
will run at is selected by the processor itself.  
```

---

## Comment 16

> Username: grisumbras  
> Created_at: 2024-07-25 20:34:05 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2251348054  

To be honest, this doesn't explain to me the canada.json phenomenon. Or this PR wasn't an attempt to investigate it?

---

## Comment 17

> Username: sdarwin  
> Created_at: 2024-07-25 20:45:33 UTC  
> Updated_at: 2024-07-25 20:45:44 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2251365400  

This PR was an attempt to analyze and debug the performance of a new server, in general. Not specific to canada.json.    
A benchmark machine should be as stable as possible, with replicable benchmarks.  
  
Now there is a new server. So far it has been mostly consistent, with variability in the twitter data set.   If that continues it could be worthwhile to examine the data and try to figure out why.

---

## Comment 18

> Username: sdarwin  
> Created_at: 2024-07-25 22:57:40 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2251535506  

Something else affecting the current benchmarks is that new machine is running Ubuntu 22.04 and clang-14.   As soon as there is a supported upgrade path to Ubuntu 24.04 and clang-18 I will upgrade.  That could improve the results. ( "It is recommended to wait until the release of 24.04.1 LTS (scheduled for August 15th, 2024) before upgrading from Ubuntu 22.04 LTS." )

---

## Comment 19

> Username: grisumbras  
> Created_at: 2024-07-26 04:45:15 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2251965793  

Interesting. It looks like on this new machine it doesn't like parsing twitter.json with clang.  
https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1031/pullrequest.html

---

## Comment 20

> Username: sdarwin  
> Created_at: 2024-07-26 09:36:23 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-2252354178  

> It looks like on this new machine it doesn't like parsing twitter.json with clang.  
  
Right. The results have a lot of randomization. Either red or green is not important there.    
However, the other machine, an i5 processor, didn't get that.  What was different? (kernel, clang, cpu). That's why the next OS upgrade could be informative.   Although "remove unnecessary branch in string serialization" seems to have an affect also.

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2025-12-23 17:41:07 UTC  
> Url: https://github.com/boostorg/json/pull/1028#issuecomment-3687455157  

GCOVR code coverage report [https://1028.json.prtest2.cppalliance.org/gcovr/index.html](https://1028.json.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://1028.json.prtest2.cppalliance.org/genhtml/index.html](https://1028.json.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://1028.json.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://1028.json.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2025-12-23 17:41:06 UTC

---
