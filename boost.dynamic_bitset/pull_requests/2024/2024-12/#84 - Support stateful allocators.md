# #84 Support stateful allocators [Merged]

> Username: aleden  
> Created at: 2024-12-05 21:57:52 UTC  
> Updated at: 2024-12-24 14:30:02 UTC  
> Merged at: 2024-12-24 14:29:42 UTC  
> Closed at: 2024-12-24 14:29:42 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/84  

If one uses `boost::interprocess::allocator` (or some other stateful allocator) compilation will fail in non-release mode (i.e. `!defined(NDEBUG)`) on two methods of dynamic_bitset (move constructor and move assignment operator). The problematic line is:  
  
```cpp  
    // Required so that assert(m_check_invariants()); works.                                                                              
    assert((b.m_bits = buffer_type()).empty());                                       
```  
  
Since construction of `buffer_type` requires an allocator argument. This patch simply passes `get_allocator()` to construct it.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-24 14:09:50 UTC  
> Updated_at: 2024-12-24 14:30:02 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/84#issuecomment-2561168948  

## [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.55%. Comparing base [(`d4be7a4`)](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/d4be7a4fcbee219f5678a3eea81f1a814a7cb5d3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2e28eb2`)](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/2e28eb2b5d5123e037eadec9996a44268d515e7c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 20 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #84      +/-   ##  
===========================================  
- Coverage    95.80%   95.55%   -0.26%       
===========================================  
  Files            5        5                
  Lines          668      675       +7       
  Branches         0      210     +210       
===========================================  
+ Hits           640      645       +5       
- Misses          28       29       +1       
- Partials         0        1       +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84?src=pr&el=tree&filepath=include%2Fboost%2Fdynamic_bitset%2Fdynamic_bitset.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `95.21% <100.00%> (-0.28%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cd59d32...2e28eb2](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/84?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
