# #551 Stream cleanup [Open]

> Username: grisumbras  
> Created at: 2021-04-28 10:57:51 UTC  
> Updated at: 2021-05-01 16:59:09 UTC  
> Url: https://github.com/boostorg/json/pull/551  

Fix #213

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-28 11:12:34 UTC  
> Updated_at: 2021-04-28 11:21:54 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-828369853  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/551?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#551](https://codecov.io/gh/boostorg/json/pull/551?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c1a299a) into [develop](https://codecov.io/gh/boostorg/json/commit/aae1863def3fb605d77ebbefc851cc26ac1758a9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (aae1863) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/551/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/551?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #551      +/-   ##  
===========================================  
- Coverage    99.10%   99.10%   -0.01%       
===========================================  
  Files           67       67                
  Lines         6058     6040      -18       
===========================================  
- Hits          6004     5986      -18       
  Misses          54       54                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/551?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/boostorg/json/pull/551/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/stream.hpp](https://codecov.io/gh/boostorg/json/pull/551/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJlYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/serializer.hpp](https://codecov.io/gh/boostorg/json/pull/551/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/serializer.ipp](https://codecov.io/gh/boostorg/json/pull/551/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `96.92% <100.00%> (-0.06%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/551?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/551?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [aae1863...c1a299a](https://codecov.io/gh/boostorg/json/pull/551?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-04-28 12:09:13 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-828401783  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/551/pullrequest-condensed-bfd83bd.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/551/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/551/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-04-28 15:47:56 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-828565047  

These affect performance

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-04-28 20:40:51 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-828763019  

Yeah, I see that. Do you know the reason? The changes should have been purely cosmetical?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-04-28 20:47:33 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-828768208  

> The changes should have been purely cosmetical?  
  
The local stream allows the compiler to assume there is no aliasing

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-04-28 20:47:54 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-828768404  

@sdkrystian knows more

---

## Comment 7

> Username: sdkrystian  
> Created_at: 2021-05-01 16:45:34 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-830660055  

@grisumbras @vinniefalco is correct -- creating the local streams allows for the compiler to assume no aliasing. Without them, it must operate under the assumption that the referenced stream may be modified by writes through aliasable pointer types within the function (or ones deeper within the call stack).

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2021-05-01 16:59:09 UTC  
> Url: https://github.com/boostorg/json/pull/551#issuecomment-830661905  

In retrospect maybe we should have left a comment on the declaration for `local_stream` explaining intent.

---
