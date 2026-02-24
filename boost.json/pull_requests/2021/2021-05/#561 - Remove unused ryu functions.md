# #561 Remove unused ryu functions [Merged]

> Username: grisumbras  
> Created at: 2021-05-03 21:28:54 UTC  
> Updated at: 2021-05-22 18:20:02 UTC  
> Merged at: 2021-05-22 18:19:54 UTC  
> Closed at: 2021-05-22 18:19:54 UTC  
> Url: https://github.com/boostorg/json/pull/561  

Fix #545

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-03 21:39:05 UTC  
> Updated_at: 2021-05-22 18:19:38 UTC  
> Url: https://github.com/boostorg/json/pull/561#issuecomment-831552403  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/561?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#561](https://codecov.io/gh/boostorg/json/pull/561?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (881b68e) into [develop](https://codecov.io/gh/boostorg/json/commit/cf7c7073618a585558315bec200ad61b9621e9b8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cf7c707) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 881b68e differs from pull request most recent head 6e69ed3. Consider uploading reports for the commit 6e69ed3 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/561/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/561?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #561      +/-   ##  
===========================================  
- Coverage    99.05%   99.05%   -0.01%       
===========================================  
  Files           68       68                
  Lines         6117     6110       -7       
===========================================  
- Hits          6059     6052       -7       
  Misses          58       58                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/561?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/ryu/impl/d2s.ipp](https://codecov.io/gh/boostorg/json/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9yeXUvaW1wbC9kMnMuaXBw) | `99.54% <ø> (-0.02%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/561?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/561?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cf7c707...6e69ed3](https://codecov.io/gh/boostorg/json/pull/561?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-05-03 23:30:22 UTC  
> Url: https://github.com/boostorg/json/pull/561#issuecomment-831598303  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest-condensed-fe60a1e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-05-04 13:14:49 UTC  
> Url: https://github.com/boostorg/json/pull/561#issuecomment-831932297  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest-condensed-6d5c465.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html)

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-05-04 15:19:38 UTC  
> Url: https://github.com/boostorg/json/pull/561#issuecomment-832025218  

So, the coverage reduction(as far as I can tell) is due to the decreased number of lines of code.  
  
The benchmark results are kinda strange: the functions that were removed weren't even used.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2021-05-04 16:47:29 UTC  
> Url: https://github.com/boostorg/json/pull/561#issuecomment-832087795  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest-condensed-ee46476.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-05-22 14:04:14 UTC  
> Url: https://github.com/boostorg/json/pull/561#issuecomment-846412927  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest-condensed-bae7df1.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/561/pullrequest.html)

---
