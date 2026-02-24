# #639 Work around incorrect GCC warning [Closed]

> Username: nbougalis  
> Created at: 2017-07-11 23:53:25 UTC  
> Updated at: 2017-07-15 04:26:13 UTC  
> Closed at: 2017-07-15 04:26:13 UTC  
> Url: https://github.com/boostorg/beast/pull/639  

Testing.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-12 00:06:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/639#pullrequestreview-49361566  

📁 include/beast/zlib/detail/deflate_stream.hpp

```diff
1062 |     else if(zs.avail_in == 0 && (
1063 |-             old_flush && flush <= *old_flush
1063 |+             old_flush && flush <= old_flush
```

> Username: vinniefalco  
> Created_at: 2017-07-12 00:06:05 UTC  
> Updated_at: 2017-07-12 23:32:27 UTC  
> Url: https://github.com/boostorg/beast/pull/639#discussion_r126837770  

Yeah that's how it was before...


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-12 00:19:54 UTC  
> Updated_at: 2017-07-13 00:22:47 UTC  
> Url: https://github.com/boostorg/beast/pull/639#issuecomment-314608305  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=h1) Report  
> Merging [#639](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/3bcd9865f80f12ba5faad35c564918f85b02e271?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/639/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #639      +/-   ##  
===========================================  
+ Coverage    93.86%   93.87%   +<.01%       
===========================================  
  Files          102      102                
  Lines         7764     7766       +2       
===========================================  
+ Hits          7288     7290       +2       
  Misses         476      476  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `92.88% <100%> (+0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=footer). Last update [3bcd986...82c6105](https://codecov.io/gh/vinniefalco/Beast/pull/639?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-07-12 00:45:30 UTC  
> Url: https://github.com/boostorg/beast/pull/639#issuecomment-314612027  

bzzzzzt wrong, thanks for playing, please try again ;) ;) ;)  
https://travis-ci.org/vinniefalco/Beast/jobs/252620909#L6266

---

## Comment 4

> Username: uecasm  
> Created_at: 2017-07-13 03:55:22 UTC  
> Url: https://github.com/boostorg/beast/pull/639#issuecomment-314963518  

Shouldn't that code instead be doing a `swap`?  Or at least using move-assignment.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-07-13 03:56:49 UTC  
> Updated_at: 2017-07-13 03:57:34 UTC  
> Url: https://github.com/boostorg/beast/pull/639#issuecomment-314963702  

**swap** or move-assign?  `Flush` is an enum

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-07-15 04:26:13 UTC  
> Url: https://github.com/boostorg/beast/pull/639#issuecomment-315508775  

Merged!

---
