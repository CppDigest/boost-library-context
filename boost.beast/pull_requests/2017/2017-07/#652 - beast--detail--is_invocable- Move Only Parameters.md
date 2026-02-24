# #652 beast::detail::is_invocable: Move Only Parameters [Closed]

> Username: RobertLeahy  
> Created at: 2017-07-15 02:33:08 UTC  
> Updated at: 2017-07-16 14:22:45 UTC  
> Closed at: 2017-07-16 14:22:45 UTC  
> Url: https://github.com/boostorg/beast/pull/652  

Previously reported that function objects taking move only types by value were not invocable.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-15 03:11:16 UTC  
> Updated_at: 2017-07-15 03:11:32 UTC  
> Url: https://github.com/boostorg/beast/pull/652#issuecomment-315505377  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/652?src=pr&el=h1) Report  
> Merging [#652](https://codecov.io/gh/vinniefalco/Beast/pull/652?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/3bcd9865f80f12ba5faad35c564918f85b02e271?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/652/graphs/tree.svg?width=650&src=pr&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/652?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #652   +/-   ##  
========================================  
  Coverage    93.86%   93.86%             
========================================  
  Files          102      102             
  Lines         7764     7764             
========================================  
  Hits          7288     7288             
  Misses         476      476  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/652?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/652?src=pr&el=footer). Last update [3bcd986...f627231](https://codecov.io/gh/vinniefalco/Beast/pull/652?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-15 03:29:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/652#pullrequestreview-50180914  

📁 test/core/type_traits.cpp

```diff
  41 |+ struct is_invocable_udt4
  42 |+ {
  43 |+     void operator()(std::unique_ptr<int>);
```

> Username: vinniefalco  
> Created_at: 2017-07-15 03:29:28 UTC  
> Url: https://github.com/boostorg/beast/pull/652#discussion_r127575504  

**NOICE!!!**


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-07-15 04:43:19 UTC  
> Url: https://github.com/boostorg/beast/pull/652#issuecomment-315509361  

This will go into **version 82**, thanks!

---
