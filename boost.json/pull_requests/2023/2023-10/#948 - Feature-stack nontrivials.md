# #948 Feature/stack nontrivials [Closed]

> Username: grisumbras  
> Created at: 2023-10-11 13:40:44 UTC  
> Updated at: 2024-10-30 12:31:27 UTC  
> Closed at: 2024-10-30 12:31:22 UTC  
> Url: https://github.com/boostorg/json/pull/948  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-10-11 14:10:15 UTC  
> Updated_at: 2023-11-13 17:27:10 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1757779956  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#948](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (91a7303) into [develop](https://app.codecov.io/gh/boostorg/json/commit/90d858fc70ddd16898c29eec58692a50fb6902d6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (90d858f) will **increase** coverage by `0.03%`.  
> The diff coverage is `99.06%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/948/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #948      +/-   ##  
===========================================  
+ Coverage    93.01%   93.04%   +0.03%       
===========================================  
  Files           87       87                
  Lines         8112     8170      +58       
===========================================  
+ Hits          7545     7602      +57       
- Misses         567      568       +1       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/stack.hpp](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdGFjay5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/impl/stack.hpp](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmhwcA==) | `99.00% <99.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/json/pull/948/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [90d858f...91a7303](https://app.codecov.io/gh/boostorg/json/pull/948?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-10-11 14:55:24 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1757876490  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-086599b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2023-10-11 16:04:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/948#pullrequestreview-1671749716  

📁 include/boost/json/detail/impl/stack.hpp

```diff
  30 |+ //--------------------------------------
  31 |+ 
  32 |+ struct stack::non_trivial
```

> Username: vinniefalco  
> Created_at: 2023-10-11 16:04:33 UTC  
> Updated_at: 2023-10-11 16:04:34 UTC  
> Url: https://github.com/boostorg/json/pull/948#discussion_r1355308752  

This looks awfully familiar, didn't I already do something similar?

> Username: grisumbras  
> Created_at: 2023-10-11 16:18:00 UTC  
> Updated_at: 2023-10-11 16:18:01 UTC  
> Url: https://github.com/boostorg/json/pull/948#discussion_r1355333174  

That's your code (yet). I'm trying several changes against automatic benchmarking. I want to find the variation of it that affects performance in the smallest way.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-10-11 16:15:21 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1758044411  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-db8b16b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-10-11 17:35:20 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1758167740  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-cf155a8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-10-11 19:05:19 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1758341518  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-1a87d28.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-10-12 09:25:17 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1759253221  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-93c71cc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-10-16 19:07:59 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1765114311  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-253a433.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-10-19 15:45:14 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1771257473  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-01c084a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-10-23 16:15:20 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1775548219  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-71880c8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-10-23 20:10:17 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1775943432  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-f0e1ba7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-10-23 21:25:13 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1776046551  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-fc41c3a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-10-24 18:50:20 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1777832487  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-92210a6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-10-25 15:25:09 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1779530949  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-b9d1de3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-10-25 20:15:16 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1779986986  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-c35c6c7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-10-26 08:42:26 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1780671868  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-169db68.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-10-26 17:45:20 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1781564175  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-1632ea0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-11-10 19:21:09 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1806310849  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-9c6fa01.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-11-13 14:56:17 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1808319524  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-73eb323.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2023-11-13 18:01:11 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1808719311  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-bf788d3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-11-14 10:21:09 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1809926552  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-715ca58.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2023-12-11 16:34:31 UTC  
> Url: https://github.com/boostorg/json/pull/948#issuecomment-1850440208  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest-condensed-42f5bda.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/948/pullrequest.html)

---
