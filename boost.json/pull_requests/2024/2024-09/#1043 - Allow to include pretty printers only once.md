# #1043 Allow to include pretty printers only once [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2024-09-19 06:25:55 UTC  
> Updated at: 2024-09-26 15:44:09 UTC  
> Closed at: 2024-09-26 15:42:39 UTC  
> Url: https://github.com/boostorg/json/pull/1043  

* some linkers will include the section many times in the executable, resulting in increased load time and warning messages inside gdb  
* allows including them only once by defining BOOST_ALL_NO_EMBEDDED_GDB_SCRIPTS, and including the printers.hpp file only once.

---

## Comment 1

> Username: Julien-Blanc-tgcm  
> Created_at: 2024-09-19 06:26:31 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2360096125  

I think this feature should be documented somewhere, which is missing currently. Which part of the doc would fit ?

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-09-19 06:32:29 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2360105039  

An automated preview of the documentation is available at [https://1043.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1043.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-09-19 07:16:17 UTC  
> Updated_at: 2024-09-26 15:44:09 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2360177671  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1043?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.41%. Comparing base [(`906bf1a`)](https://app.codecov.io/gh/boostorg/json/commit/906bf1a70b6e97ed808206fb225cfa52987541dd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`fb0d5c1`)](https://app.codecov.io/gh/boostorg/json/commit/fb0d5c17c7e3c7f23ba5a983c307580a6e0086aa?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1043/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1043?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1043   +/-   ##  
========================================  
  Coverage    93.41%   93.41%             
========================================  
  Files           91       91             
  Lines         8672     8672             
========================================  
  Hits          8101     8101             
  Misses         571      571             
```  
  
| [Flag](https://app.codecov.io/gh/boostorg/json/pull/1043/flags?src=pr&el=flags&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [](https://app.codecov.io/gh/boostorg/json/pull/1043/flags?src=pr&el=flag&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | `93.41% <ø> (ø)` | |  
  
Flags with carried forward coverage won't be shown. [Click here](https://docs.codecov.io/docs/carryforward-flags?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#carryforward-flags-in-the-pull-request-comment) to find out more.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1043?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/config.hpp](https://app.codecov.io/gh/boostorg/json/pull/1043?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fconfig.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1043?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1043?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [906bf1a...fb0d5c1](https://app.codecov.io/gh/boostorg/json/pull/1043?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-09-19 07:35:06 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2360214190  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1043/pullrequest-condensed-593cce6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1043/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1043/pullrequest.html)

---

## Comment 5

> Username: grisumbras  
> Created_at: 2024-09-20 14:00:10 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2363811007  

It's hard to say. I planned to write a dedicated section on error handling, debugging and so on. I'll describe your technique there.

---

## Comment 6

> Username: grisumbras  
> Created_at: 2024-09-20 14:23:31 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2363859761  

Is there any reason to have both `json/printers.hpp` and `json/detail/gdb_printers.hpp`?

---

## Comment 7

> Username: Julien-Blanc-tgcm  
> Created_at: 2024-09-20 14:53:45 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2363922336  

I don't like the idea of asking users to explicitly include a file in the detail directory. Also, i think it is better not to name the file gdb, as we could include more printers there (ie, for visual studio).  
  
However i realize that calling it just printers.hpp may be misleading. Perhaps i should rename it to debug_printers.hpp .

---

## Comment 8

> Username: grisumbras  
> Created_at: 2024-09-20 14:57:15 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2363930030  

`debug_printers.hpp` sounds all right.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-09-20 15:13:24 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2363963190  

An automated preview of the documentation is available at [https://1043.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1043.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-09-20 16:15:03 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2364075792  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1043/pullrequest-condensed-cdac77c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1043/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1043/pullrequest.html)

---

## Comment 11

> Username: grisumbras  
> Created_at: 2024-09-26 15:42:17 UTC  
> Updated_at: 2024-09-26 15:43:23 UTC  
> Url: https://github.com/boostorg/json/pull/1043#issuecomment-2377326915  

Merged via 743be7fe13bbea76f651ab81703a5b8e1fdf25e0  
  
Thank you for your contribution.

---
