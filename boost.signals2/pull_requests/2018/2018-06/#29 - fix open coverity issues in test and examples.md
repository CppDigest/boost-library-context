# #29 fix open coverity issues in test and examples [Merged]

> Username: jeking3  
> Created at: 2018-06-15 01:45:27 UTC  
> Updated at: 2019-03-28 00:02:00 UTC  
> Merged at: 2018-06-16 19:55:21 UTC  
> Closed at: 2018-06-16 19:55:21 UTC  
> Url: https://github.com/boostorg/signals2/pull/29  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-06-15 02:58:56 UTC  
> Updated_at: 2018-06-15 03:01:57 UTC  
> Url: https://github.com/boostorg/signals2/pull/29#issuecomment-397499047  

# [Codecov](https://codecov.io/gh/boostorg/signals2/pull/29?src=pr&el=h1) Report  
> Merging [#29](https://codecov.io/gh/boostorg/signals2/pull/29?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/signals2/commit/b2767c46b308d919f321d4288786cc3ccc0231a7?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/signals2/pull/29/graphs/tree.svg?height=150&width=650&token=s6LljlwIKV&src=pr)](https://codecov.io/gh/boostorg/signals2/pull/29?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #29   +/-   ##  
========================================  
  Coverage    53.13%   53.13%             
========================================  
  Files           29       29             
  Lines          862      862             
  Branches       337      337             
========================================  
  Hits           458      458             
  Misses          91       91             
  Partials       313      313  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/signals2/pull/29?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/signals2/pull/29?src=pr&el=footer). Last update [b2767c4...ba8f4f2](https://codecov.io/gh/boostorg/signals2/pull/29?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2019-03-26 14:13:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/signals2/pull/29#pullrequestreview-218918006  

📁 example/doc_view_acm_deconstruct.cpp

```diff
  77 |   // private constructor to force use of deconstruct
  78 |-   TextView()
  78 |+   TextView() : m_document(0)
```

> Username: jeking3  
> Created_at: 2019-03-26 14:13:23 UTC  
> Updated_at: 2019-03-26 14:13:24 UTC  
> Url: https://github.com/boostorg/signals2/pull/29#discussion_r269123186  

There's a second instance of this that needs to be applied on line 115.

> Username: fmhess  
> Created_at: 2019-03-28 00:02:00 UTC  
> Url: https://github.com/boostorg/signals2/pull/29#discussion_r269816347  

It should be fixed now on develop branch.


---
