# #1414 Remove linking with Boost.System [Closed]

> Username: Lastique  
> Created at: 2019-01-14 18:47:34 UTC  
> Updated at: 2019-01-16 00:13:34 UTC  
> Closed at: 2019-01-15 20:31:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1414  

Since Boost.System is header-only now, no need to link with the library.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-01-14 20:31:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1414#issuecomment-454150583  

I can't really find any fault with this :)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-01-15 02:42:35 UTC  
> Updated_at: 2019-01-15 15:47:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1414#issuecomment-454247304  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1414?src=pr&el=h1) Report  
> Merging [#1414](https://codecov.io/gh/boostorg/beast/pull/1414?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0822e1bb5e8000ab4df7fd09a31e16912254b8d4?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1414/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1414?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1414      +/-   ##  
===========================================  
+ Coverage    93.04%   93.05%   +<.01%       
===========================================  
  Files          119      119                
  Lines        11241    11241                
===========================================  
+ Hits         10459    10460       +1       
+ Misses         782      781       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1414?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `89.1% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1414?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1414?src=pr&el=footer). Last update [0822e1b...ee6c544](https://codecov.io/gh/boostorg/beast/pull/1414?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-01-15 09:33:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1414#issuecomment-454325631  

Linking with the library adds a runtime dependency on the Boost.System binary, which is no longer needed. Since you're not building a shared library, it may not seem like a big deal, except for users that use Boost.Build to build their projects with Boost.Beast.  
  
There is no reason to link with Boost.System, and IMO ultimately we should move towards removing the library.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2019-01-15 13:58:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1414#issuecomment-454400716  

Note to self: README.md needs to be updated before merge

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-01-15 15:47:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1414#issuecomment-454440477  

I've added a commit updating the readme as well.

---
