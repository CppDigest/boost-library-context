# #141 Refactor stream aliasing, suspend, and error paths [Merged]

> Username: sdkrystian  
> Created at: 2020-08-10 18:23:26 UTC  
> Updated at: 2020-08-22 18:00:40 UTC  
> Merged at: 2020-08-19 00:36:38 UTC  
> Closed at: 2020-08-19 00:36:38 UTC  
> Url: https://github.com/boostorg/json/pull/141  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-11 08:37:49 UTC  
> Updated_at: 2020-08-18 23:52:04 UTC  
> Url: https://github.com/boostorg/json/pull/141#issuecomment-671812727  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/141?src=pr&el=h1) Report  
> Merging [#141](https://codecov.io/gh/CPPAlliance/json/pull/141?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/36c4a1a78740cf4fd8175aaca82f40bee0e2c425&el=desc) will **increase** coverage by `0.04%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/141/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/141?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #141      +/-   ##  
===========================================  
+ Coverage    98.35%   98.39%   +0.04%       
===========================================  
  Files           64       62       -2       
  Lines         5845     5685     -160       
===========================================  
- Hits          5749     5594     -155       
+ Misses          96       91       -5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/141?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/config.hpp](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/sse2.hpp](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/stream.hpp](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJlYW0uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
| ... and [5 more](https://codecov.io/gh/CPPAlliance/json/pull/141/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/141?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/141?src=pr&el=footer). Last update [36c4a1a...97d79d2](https://codecov.io/gh/CPPAlliance/json/pull/141?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: sdkrystian  
> Created_at: 2020-08-12 20:38:35 UTC  
> Updated_at: 2020-08-14 02:11:27 UTC  
> Url: https://github.com/boostorg/json/pull/141#issuecomment-673098646  

A few changes that should be made:  
- Pass/return `const_stream` instead of `const char*`. This will require an extra `mov` for calls, but it eliminates a `mov` from memory (likely cache, but still slower) each time a function is called.  
-  Make `end_` `const` and don't utilize it in assignment/copy construction  
- Set `p_` to `end_` to indicate failure, and write the point at which parsing stopped to a data member.  
  
EDIT: passing & returning `const_stream` doesn't actually help.

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-17 17:26:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/141#pullrequestreview-468676072  

📁 include/boost/json/basic_parser.hpp

```diff
 244 |+ fail(
 245 |+     const char* p, 
 246 |+     error err) noexcept
```

> Username: vinniefalco  
> Created_at: 2020-08-17 17:26:25 UTC  
> Updated_at: 2020-08-18 23:51:56 UTC  
> Url: https://github.com/boostorg/json/pull/141#discussion_r471644359  

`error ev`


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-17 17:49:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/141#pullrequestreview-468691933  

📁 include/boost/json/basic_parser.hpp

```diff
 312 |-         if(BOOST_JSON_UNLIKELY(! end_tok))
 364 |+         cs = remain ? static_cast<const char*>(
 365 |+             std::memchr(cs.begin(), '\n', remain)) : nullptr;
```

> Username: vinniefalco  
> Created_at: 2020-08-17 17:49:54 UTC  
> Updated_at: 2020-08-18 23:51:56 UTC  
> Url: https://github.com/boostorg/json/pull/141#discussion_r471659123  

```  
if( remain > 0 )  
  cs = static_cast<const char*>(  
    std::memchr(cs.data(), '\n', remain) );  
else  
  cs = nullptr;  
```

> Username: sdkrystian  
> Created_at: 2020-08-17 22:42:23 UTC  
> Updated_at: 2020-08-18 23:51:56 UTC  
> Url: https://github.com/boostorg/json/pull/141#discussion_r471813952  

Yeah, I'll be moving this out of the function.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-18 22:49:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/141#pullrequestreview-469881691  

📁 include/boost/json/detail/basic_parser.hpp

```diff
 142 |+     inline
 143 |+     const char*
 144 |+     propagate(state st);
```

> Username: vinniefalco  
> Created_at: 2020-08-18 22:49:13 UTC  
> Updated_at: 2020-08-18 23:51:56 UTC  
> Url: https://github.com/boostorg/json/pull/141#discussion_r472535777  

This is not descriptive enough

> Username: sdkrystian  
> Created_at: 2020-08-18 22:57:54 UTC  
> Updated_at: 2020-08-18 23:51:56 UTC  
> Url: https://github.com/boostorg/json/pull/141#discussion_r472538670  

IMO it's the least misleading. I'll add docs saying what it actually does.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-18 22:56:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/141#pullrequestreview-469884365  

📁 include/boost/json/basic_parser.hpp

```diff
 288 |-         if(BOOST_JSON_UNLIKELY(! end_tok))
 370 |+         cs = remain ? static_cast<const char*>(
 371 |+             std::memchr(cs.begin(), '\n', remain)) : canary();
```

> Username: vinniefalco  
> Created_at: 2020-08-18 22:56:03 UTC  
> Updated_at: 2020-08-18 23:51:56 UTC  
> Url: https://github.com/boostorg/json/pull/141#discussion_r472538077  

no ternary please

> Username: sdkrystian  
> Created_at: 2020-08-18 22:57:17 UTC  
> Updated_at: 2020-08-18 23:51:56 UTC  
> Url: https://github.com/boostorg/json/pull/141#discussion_r472538477  

Going to address this in another commit, later on


---
