# #42 Improve coverage for string [Closed]

> Username: sdkrystian  
> Created at: 2020-02-14 23:23:34 UTC  
> Updated at: 2020-02-26 00:42:21 UTC  
> Closed at: 2020-02-26 00:42:21 UTC  
> Url: https://github.com/boostorg/json/pull/42  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-15 00:11:45 UTC  
> Updated_at: 2020-02-21 18:19:09 UTC  
> Url: https://github.com/boostorg/json/pull/42#issuecomment-586527587  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/42?src=pr&el=h1) Report  
> Merging [#42](https://codecov.io/gh/vinniefalco/json/pull/42?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/b2fef3d067335a904b86a2a93d432b230e1bc96d?src=pr&el=desc) will **increase** coverage by `0.88%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/42/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/42?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #42      +/-   ##  
===========================================  
+ Coverage     98.2%   99.09%   +0.88%       
===========================================  
  Files           57       57                
  Lines         4856     4854       -2       
===========================================  
+ Hits          4769     4810      +41       
+ Misses          87       44      -43  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/json/pull/42?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/string.hpp](https://codecov.io/gh/vinniefalco/json/pull/42/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/json/detail/impl/string\_impl.ipp](https://codecov.io/gh/vinniefalco/json/pull/42/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0cmluZ19pbXBsLmlwcA==) | `99.01% <100%> (+21.18%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/42?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/42?src=pr&el=footer). Last update [b2fef3d...5d1a185](https://codecov.io/gh/vinniefalco/json/pull/42?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-21 01:16:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/42#pullrequestreview-362345803  

📁 include/boost/json/detail/impl/string_impl.ipp

```diff
 254 |         // there is nothing to replace; return
 255 |-         if (inside && s - curr_data == pos && n1 == n2)
 255 |+         if (inside && std::size_t(s - curr_data) == pos && n1 == n2)
```

> Username: vinniefalco  
> Created_at: 2020-02-21 01:16:44 UTC  
> Updated_at: 2020-02-21 16:36:07 UTC  
> Url: https://github.com/boostorg/json/pull/42#discussion_r382348992  

Can we please use `static_cast` so it sticks out?

> Username: sdkrystian  
> Created_at: 2020-02-21 02:32:14 UTC  
> Updated_at: 2020-02-21 16:37:04 UTC  
> Url: https://github.com/boostorg/json/pull/42#discussion_r382367348  

Alright, this has been addressed


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-02-26 00:42:21 UTC  
> Url: https://github.com/boostorg/json/pull/42#issuecomment-591170381  

Merged, thanks

---
