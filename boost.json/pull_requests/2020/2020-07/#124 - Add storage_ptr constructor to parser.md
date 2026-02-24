# #124 Add storage_ptr constructor to parser [Closed]

> Username: sdkrystian  
> Created at: 2020-07-01 04:15:06 UTC  
> Updated at: 2020-07-22 20:55:19 UTC  
> Closed at: 2020-07-16 21:40:16 UTC  
> Url: https://github.com/boostorg/json/pull/124  

Closes #79

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-02 02:16:40 UTC  
> Updated_at: 2020-07-16 18:58:54 UTC  
> Url: https://github.com/boostorg/json/pull/124#issuecomment-652739382  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/124?src=pr&el=h1) Report  
> Merging [#124](https://codecov.io/gh/CPPAlliance/json/pull/124?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/9b184b4dc4a39630e9ae6892f69b16f96c91bf3a&el=desc) will **increase** coverage by `0.21%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/124/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/124?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #124      +/-   ##  
===========================================  
+ Coverage    98.99%   99.21%   +0.21%       
===========================================  
  Files           59       60       +1       
  Lines         5183     5226      +43       
===========================================  
+ Hits          5131     5185      +54       
+ Misses          52       41      -11       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/124?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `99.65% <100.00%> (+1.06%)` | :arrow_up: |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `99.21% <0.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/sse2.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/buffer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9idWZmZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/value\_cast.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9jYXN0LmhwcA==) | | |  
| [include/boost/json/to\_value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3RvX3ZhbHVlLmhwcA==) | | |  
| [include/boost/json/detail/to\_value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC90b192YWx1ZS5ocHA=) | | |  
| [include/boost/json/value\_cast.hpp](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Nhc3QuaHBw) | | |  
| ... and [8 more](https://codecov.io/gh/CPPAlliance/json/pull/124/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/124?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/124?src=pr&el=footer). Last update [9b184b4...ef13c97](https://codecov.io/gh/CPPAlliance/json/pull/124?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-03 18:53:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/124#pullrequestreview-442535716  

📁 include/boost/json/parser.hpp

```diff
 118 |+     */
 119 |+     BOOST_JSON_DECL
 120 |+     explicit parser(storage_ptr sp);
```

> Username: vinniefalco  
> Created_at: 2020-07-03 18:53:01 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r449682302  

explicit on its own line please

> Username: vinniefalco  
> Created_at: 2020-07-03 18:53:50 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r449682426  

We should call it "intermediate storage" and make it clear that this is used internally and not for the final DOM. We also need to explain that it is reused between parses, which improves performance.

> Username: sdkrystian  
> Created_at: 2020-07-16 15:14:28 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r455864362  

Will do


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-07-03 18:54:20 UTC  
> Updated_at: 2020-07-03 18:54:54 UTC  
> Url: https://github.com/boostorg/json/pull/124#issuecomment-653649807  

The usage of this constructor needs to be explained in the documentation, along with the implications for performance. The performance profile of the default constructor also needs to be explained (that it gets its reusable, intermediate storage from the default memory resource).

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-15 22:05:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/124#pullrequestreview-449376659  

📁 bench/bench.cpp

```diff
 320 |-         parser p;
 320 |+         monotonic_resource mr1;
 321 |+         parser p(&mr1);
```

> Username: vinniefalco  
> Created_at: 2020-07-15 22:05:15 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r455392086  

We shouldn't be doing this , otherwise it won't free the memory when the stack is reallocated. It needs to use the default resource.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-15 22:05:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/124#pullrequestreview-449376747  

📁 include/boost/json/impl/parser.ipp

```diff
 156 | parser::
 157 |- parser()
 157 |+ parser()
```

> Username: vinniefalco  
> Created_at: 2020-07-15 22:05:26 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r455392167  

should this be noexcept?

> Username: sdkrystian  
> Created_at: 2020-07-16 15:19:09 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r455867715  

Yup


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-15 22:05:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/124#pullrequestreview-449376807  

📁 include/boost/json/impl/parser.ipp

```diff
 161 |+ 
 162 |+ parser::
 163 |+ parser(storage_ptr sp)
```

> Username: vinniefalco  
> Created_at: 2020-07-15 22:05:33 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r455392236  

should this be noexcept?

> Username: sdkrystian  
> Created_at: 2020-07-16 15:18:59 UTC  
> Updated_at: 2020-07-16 15:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r455867601  

Yup


---

## Comment 7

> Username: sdkrystian  
> Created_at: 2020-07-16 15:20:51 UTC  
> Url: https://github.com/boostorg/json/pull/124#issuecomment-659482978  

@vinniefalco In addition to your suggested changes I'll be adding a constructor of this form to `serializer`.

---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-16 19:34:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/124#pullrequestreview-450156439  

📁 include/boost/json/parser.hpp

```diff
 115 |+     /** Constructor.
 116 |+ 
 117 |+         Constructs a empty parser using the supplied
```

> Username: vinniefalco  
> Created_at: 2020-07-16 19:34:11 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r456026500  

Nitpick: I would say specified instead of supplied. But it is too small a distinction to bother changing right now.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-16 19:34:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/124#pullrequestreview-450156597  

📁 include/boost/json/parser.hpp

```diff
 126 |+ 
 127 |+         The `sp` parameter is only used to
 128 |+         allocate intermediate storage; will not be used
```

> Username: vinniefalco  
> Created_at: 2020-07-16 19:34:25 UTC  
> Url: https://github.com/boostorg/json/pull/124#discussion_r456026682  

grammar is funky here but we can roll with it.


---
