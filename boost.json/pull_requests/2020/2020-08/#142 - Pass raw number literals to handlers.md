# #142 Pass raw number literals to handlers [Closed]

> Username: sdkrystian  
> Created at: 2020-08-10 18:38:42 UTC  
> Updated at: 2020-08-22 17:59:35 UTC  
> Closed at: 2020-08-12 00:25:40 UTC  
> Url: https://github.com/boostorg/json/pull/142  

Needs tests

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-10 18:52:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/142#pullrequestreview-464488178  

📁 include/boost/json/basic_parser.hpp

```diff
2494 |+         if(BOOST_JSON_UNLIKELY(
2495 |+             ! h.on_number_part(
2496 |+                 {begin, cs.used(begin)}, ec_)))
```

> Username: vinniefalco  
> Created_at: 2020-08-10 18:52:02 UTC  
> Url: https://github.com/boostorg/json/pull/142#discussion_r468109366  

@glenfe @pdimov As written, the init-list allows `{begin, cs.used(begin)}` allows `on_number_part` to be declared with an argument type that may not be compatible with string view. Is this desirable? Or should the code above be written as:  
```  
            ! h.on_number_part(  
                string_view(begin, cs.used(begin)),  
                ec_)))  
```

> Username: pdimov  
> Created_at: 2020-08-10 20:18:50 UTC  
> Updated_at: 2020-08-10 20:18:51 UTC  
> Url: https://github.com/boostorg/json/pull/142#discussion_r468162350  

Hard to say. If we require `on_number_part` to take exactly a `string_view` parameter, the above is fine. If we always pass a `string_view`, this allows `on_number_part` to take something convertible from `string_view`, which is probably also undesirable.

> Username: vinniefalco  
> Created_at: 2020-08-11 13:31:23 UTC  
> Url: https://github.com/boostorg/json/pull/142#discussion_r468580647  

I haven't bothered to write code to force the handler to have particular signatures (or else get a compile errror) because it doesn't seem worth it.

> Username: sdkrystian  
> Created_at: 2020-08-11 18:08:29 UTC  
> Updated_at: 2020-08-11 18:08:30 UTC  
> Url: https://github.com/boostorg/json/pull/142#discussion_r468769107  

IMO it's worth it because by restricting the signature we can potentially optimize around it.

> Username: vinniefalco  
> Created_at: 2020-08-11 19:16:39 UTC  
> Url: https://github.com/boostorg/json/pull/142#discussion_r468808649  

we can restrict the signature without writing any code, just say it in the docs "the signature must match"


---

## Comment 2

> Username: sdkrystian  
> Created_at: 2020-08-10 20:59:19 UTC  
> Url: https://github.com/boostorg/json/pull/142#issuecomment-671586677  

Addresses #101

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-08-11 11:43:32 UTC  
> Updated_at: 2020-08-11 13:14:09 UTC  
> Url: https://github.com/boostorg/json/pull/142#issuecomment-671896226  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/142?src=pr&el=h1) Report  
> Merging [#142](https://codecov.io/gh/CPPAlliance/json/pull/142?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/ec29dfa768a16672534f758005dfe4841a8ea93d&el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/142/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/142?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #142   +/-   ##  
========================================  
  Coverage    98.34%   98.35%             
========================================  
  Files           63       63             
  Lines         5754     5781   +27       
========================================  
+ Hits          5659     5686   +27       
  Misses          95       95             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/142?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/142/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/142/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/142/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/142/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/142?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/142?src=pr&el=footer). Last update [ec29dfa...d41ce22](https://codecov.io/gh/CPPAlliance/json/pull/142?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-08-11 23:04:20 UTC  
> Url: https://github.com/boostorg/json/pull/142#issuecomment-672342066  

Approved

---
