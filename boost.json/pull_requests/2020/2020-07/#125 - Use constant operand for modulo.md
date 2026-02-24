# #125 Use constant operand for modulo [Closed]

> Username: sdkrystian  
> Created at: 2020-07-02 00:11:39 UTC  
> Updated at: 2020-07-22 20:54:53 UTC  
> Closed at: 2020-07-16 21:40:16 UTC  
> Url: https://github.com/boostorg/json/pull/125  

Implements #115

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-07-02 01:01:34 UTC  
> Url: https://github.com/boostorg/json/pull/125#issuecomment-652719158  

The commit message body should have the text `fix #115`

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 01:03:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/125#pullrequestreview-441284630  

📁 include/boost/json/detail/object_impl.hpp

```diff
 137 | 
 138 |+     //------------------------------------------------
 139 |+     // https://github.com/CPPAlliance/json/issues/115
```

> Username: vinniefalco  
> Created_at: 2020-07-02 01:03:27 UTC  
> Updated_at: 2020-07-03 18:04:32 UTC  
> Url: https://github.com/boostorg/json/pull/125#discussion_r448692265  

dont need this comment


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 01:03:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/125#pullrequestreview-441284690  

📁 include/boost/json/detail/object_impl.hpp

```diff
 147 |+     inline
 148 |+     std::size_t
 149 |+     position(
```

> Username: vinniefalco  
> Created_at: 2020-07-02 01:03:40 UTC  
> Updated_at: 2020-07-03 18:04:32 UTC  
> Url: https://github.com/boostorg/json/pull/125#discussion_r448692309  

need a better name for this

> Username: sdkrystian  
> Created_at: 2020-07-03 17:58:02 UTC  
> Updated_at: 2020-07-03 18:04:32 UTC  
> Url: https://github.com/boostorg/json/pull/125#discussion_r449672236  

`bucket_index`?


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 01:04:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/125#pullrequestreview-441284834  

📁 include/boost/json/detail/object_impl.hpp

```diff
 142 |+     inline
 143 |+     prime_list&
 144 |+     primes() noexcept;
```

> Username: vinniefalco  
> Created_at: 2020-07-02 01:04:13 UTC  
> Updated_at: 2020-07-03 18:04:32 UTC  
> Url: https://github.com/boostorg/json/pull/125#discussion_r448692449  

should this be called `bucket_sizes`?


---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2020-07-02 04:46:39 UTC  
> Updated_at: 2020-07-03 20:37:02 UTC  
> Url: https://github.com/boostorg/json/pull/125#issuecomment-652778638  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/125?src=pr&el=h1) Report  
> Merging [#125](https://codecov.io/gh/CPPAlliance/json/pull/125?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/14bc67b63f5ce6ae5a119e2957e1f44f15277f5b&el=desc) will **decrease** coverage by `1.17%`.  
> The diff coverage is `26.74%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/125/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/125?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #125      +/-   ##  
===========================================  
- Coverage    99.21%   98.04%   -1.18%       
===========================================  
  Files           60       60                
  Lines         5244     5321      +77       
===========================================  
+ Hits          5203     5217      +14       
- Misses          41      104      +63       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/125?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/object\_impl.hpp](https://codecov.io/gh/CPPAlliance/json/pull/125/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL29iamVjdF9pbXBsLmhwcA==) | `50.78% <17.10%> (-49.22%)` | :arrow_down: |  
| [include/boost/json/detail/impl/object\_impl.ipp](https://codecov.io/gh/CPPAlliance/json/pull/125/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL29iamVjdF9pbXBsLmlwcA==) | `92.85% <100.00%> (ø)` | |  
| [include/boost/json/detail/object\_impl.hpp](https://codecov.io/gh/CPPAlliance/json/pull/125/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9vYmplY3RfaW1wbC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/CPPAlliance/json/pull/125/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `99.66% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/125?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/125?src=pr&el=footer). Last update [14bc67b...7426cf5](https://codecov.io/gh/CPPAlliance/json/pull/125?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2020-07-03 18:50:04 UTC  
> Url: https://github.com/boostorg/json/pull/125#issuecomment-653648132  

These changes look good but we need to explain the benchmark results.

---

## Comment 7

> Username: sdkrystian  
> Created_at: 2020-07-16 15:22:44 UTC  
> Url: https://github.com/boostorg/json/pull/125#issuecomment-659484201  

@vinniefalco Explain in what way? Like a comment as to why this is better?

---
