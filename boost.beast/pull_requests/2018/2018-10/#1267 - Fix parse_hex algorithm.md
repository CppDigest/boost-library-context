# #1267 Fix parse_hex algorithm [Closed]

> Username: compmaniak  
> Created at: 2018-10-14 22:06:51 UTC  
> Updated at: 2018-10-20 20:21:48 UTC  
> Closed at: 2018-10-20 20:21:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1267  

Current `parse_hex` implementation doesn't work at the extreme case when input is "fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff...". The function consumes all of such chars but returns maximum value for the specified type. This happens because of bounds checking via integer overflow.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-10-15 00:58:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1267#issuecomment-429679144  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1267?src=pr&el=h1) Report  
> Merging [#1267](https://codecov.io/gh/boostorg/beast/pull/1267?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/ec1c8ada7a22b274cf6e58c019c2bbe6965f6232?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `90%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1267/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1267?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1267      +/-   ##  
===========================================  
- Coverage    95.43%   95.43%   -0.01%       
===========================================  
  Files          112      112                
  Lines        11072    11074       +2       
===========================================  
+ Hits         10567    10568       +1       
- Misses         505      506       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1267?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/detail/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/1267/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `89.88% <90%> (-0.23%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1267?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1267?src=pr&el=footer). Last update [ec1c8ad...dca5f3e](https://codecov.io/gh/boostorg/beast/pull/1267?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-10-20 14:14:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1267#issuecomment-431585489  

Good find! I will add a test to confirm. Can you please give me more details? What is the exact string, is it `"fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"`? How many `f` are in there? And what is the "specified type" you are using?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2018-10-20 15:41:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1267#issuecomment-431592617  

I understand the problem now. Good work

---
