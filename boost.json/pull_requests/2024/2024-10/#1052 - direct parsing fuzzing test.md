# #1052 direct parsing fuzzing test [Merged]

> Username: tyler92  
> Created at: 2024-10-08 07:06:27 UTC  
> Updated at: 2024-10-14 17:52:17 UTC  
> Merged at: 2024-10-14 16:17:19 UTC  
> Closed at: 2024-10-14 16:17:19 UTC  
> Url: https://github.com/boostorg/json/pull/1052  

Two issues were found by the new fuzzing target: https://github.com/boostorg/json/issues/1047 and https://github.com/boostorg/json/issues/1048  
  
I think detecting memory issues in case of new changes or refactoring might be useful.  
I'm not sure about code formatting for the project, I will appreciate your feedback.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-10-08 07:12:28 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2399028166  

An automated preview of the documentation is available at [https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-10-08 07:27:27 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2399057831  

An automated preview of the documentation is available at [https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-10-08 08:09:26 UTC  
> Updated_at: 2024-10-14 17:52:17 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2399150456  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1052?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.41%. Comparing base [(`502ac79`)](https://app.codecov.io/gh/boostorg/json/commit/502ac792c7858f0c760a83eaf333fd57edd52bcb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3f88a33`)](https://app.codecov.io/gh/boostorg/json/commit/3f88a33518d28dad225c9c91d5e959121b711bc9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1052/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1052?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1052   +/-   ##  
========================================  
  Coverage    93.41%   93.41%             
========================================  
  Files           91       91             
  Lines         8667     8667             
========================================  
  Hits          8096     8096             
  Misses         571      571             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1052?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1052?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [502ac79...3f88a33](https://app.codecov.io/gh/boostorg/json/pull/1052?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-10-08 08:29:52 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2399198555  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest-condensed-2ee4823.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-10-08 10:37:23 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2399485833  

An automated preview of the documentation is available at [https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-10-08 11:39:53 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2399607993  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest-condensed-67bab7c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html)

---

## Comment 7

> Username: grisumbras  
> Created_at: 2024-10-09 13:06:59 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2402289886  

While the idea is interesting, I am sceptical fuzzing with random strings can be effective when the parser expects a very specific structure of the input. It will probably always fail on the second (`on_object_begin`), the third (`on_key`), or the fourth (`on_bool`) event.

---

## Comment 8

> Username: tyler92  
> Created_at: 2024-10-09 13:18:54 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2402329768  

According to coverage reports, it works well enough to find valid inputs, these two issues were found quite quickly. We can provide a dictionary for the fuzzer to help it a bit if we want to improve the performance.

---

## Comment 9

> Username: sdarwin  
> Created_at: 2024-10-09 15:32:01 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2402665700  

It may be a coincidence. This pull request mentions "detecting memory issues". The drone agent on IBM s390 crashed with out-of-memory errors.  I have rebooted the server.

---

## Comment 10

> Username: grisumbras  
> Created_at: 2024-10-09 15:38:52 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2402681398  

It's also possible that linking the new fuzzer requires too much memory (variant parsers do do that).

---

## Comment 11

> Username: tyler92  
> Created_at: 2024-10-09 19:05:24 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2403181100  

> I am sceptical fuzzing with random strings can be effective when the parser expects a very specific structure of the input. It will probably always fail on the second (on_object_begin), the third (on_key), or the fourth (on_bool) event.  
  
Just for an experiment: I reverted the fix for https://github.com/boostorg/json/issues/1047 and measured the time required to find that bug. Without a dictionary and with an empty corpus (the worst scenario) it takes about 10 seconds. The reason is the fuzzer doesn't have to find a valid input with all required fields. It's enough to generate a JSON with some fields to verify parser for a specific type only.  
  
Adding corpus doesn't improve speed much. But I've added two valid JSONs to seed files, and it gives the fuzzer the ability to reach the "success" point. With this change, the fuzzer finds https://github.com/boostorg/json/issues/1047 in less than one second. Also, I changed C++ version to C++14 to avoid potential OOM.  
  
Please let me know if it makes sense

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-10-09 19:12:25 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2403219845  

An automated preview of the documentation is available at [https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-10-09 20:14:55 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2403349146  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest-condensed-c89d093.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html)

---

## Review 14 [Commented]

> Username: grisumbras  
> Created_at: 2024-10-10 08:05:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/1052#pullrequestreview-2359427204  

📁 fuzzing/fuzz_direct_parse.cpp

```diff
  49 |+ 
  50 |+ #ifndef BOOST_NO_CXX17_HDR_VARIANT
  51 |+     std::variant<bool, std::uint64_t, std::int64_t, double, std::string> v;
```

> Username: grisumbras  
> Created_at: 2024-10-10 08:05:36 UTC  
> Url: https://github.com/boostorg/json/pull/1052#discussion_r1794925210  

Please, replace with `boost::variant2::variant`. It will reduce the amount of macro switching necessary.

> Username: tyler92  
> Created_at: 2024-10-10 19:13:23 UTC  
> Url: https://github.com/boostorg/json/pull/1052#discussion_r1795963383  

Done


---

## Review 15 [Commented]

> Username: grisumbras  
> Created_at: 2024-10-10 08:11:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/1052#pullrequestreview-2359439232  

📁 .github/workflows/run_fuzzer.yml

```diff
  61 |         path: 'head'
  62 |         toolset: clang-18
  63 |+         cxxstd: 14
```

> Username: grisumbras  
> Created_at: 2024-10-10 08:11:04 UTC  
> Url: https://github.com/boostorg/json/pull/1052#discussion_r1794932671  

Why have you reduced this from 17 to 14?

> Username: tyler92  
> Created_at: 2024-10-10 19:17:03 UTC  
> Url: https://github.com/boostorg/json/pull/1052#discussion_r1795966999  

I wanted to make sure this MR has a chance to be approved and avoid potential OOMs on the drone agent before (although I'm not sure if it's related to the current MR). I reverted to 17; please let me know if there are any issues with it.


---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2024-10-10 19:22:24 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2405871229  

An automated preview of the documentation is available at [https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2024-10-10 20:24:53 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2405976161  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest-condensed-8753c55.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html)

---

## Review 18 [Approved]

> Username: grisumbras  
> Created_at: 2024-10-13 11:23:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/json/pull/1052#pullrequestreview-2364896128  

Everything looks good. Can you please squsah and rebase on the current develop (if it's not already). I can also do it manually myself, but then the PR won't be considered merged by GitHub.

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2024-10-13 13:27:24 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2408980276  

An automated preview of the documentation is available at [https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2024-10-13 13:32:39 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2408982099  

An automated preview of the documentation is available at [https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1052.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 21

> Username: tyler92  
> Created_at: 2024-10-13 14:01:57 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2408992105  

> Can you please squsah and rebase on the current develop (if it's not already). I can also do it manually myself, but then the PR won't be considered merged by GitHub.  
  
Done

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2024-10-13 14:30:01 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2409001891  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest-condensed-61f0a90.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1052/pullrequest.html)

---

## Comment 23

> Username: grisumbras  
> Created_at: 2024-10-14 16:17:38 UTC  
> Url: https://github.com/boostorg/json/pull/1052#issuecomment-2411706516  

Thank you for your contribution.

---
