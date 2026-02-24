# #2593 Added source-location to all ecs. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-12-17 10:22:42 UTC  
> Updated at: 2022-12-20 05:16:46 UTC  
> Merged at: 2022-12-20 05:16:39 UTC  
> Closed at: 2022-12-20 05:16:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2593  

Closes #2475.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-17 10:52:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2593#issuecomment-1356180452  

![pullrequest](https://2593.beast.tracing.cppalliance.org/pullrequest-3e268760.png)  
Timeline tracing charts: [https://2593.beast.tracing.cppalliance.org/index.html](https://2593.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-18 07:13:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2593#issuecomment-1356712317  

![pullrequest](https://2593.beast.tracing.cppalliance.org/pullrequest-3b9b04ad.png)  
Timeline tracing charts: [https://2593.beast.tracing.cppalliance.org/index.html](https://2593.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-12-18 07:42:52 UTC  
> Updated_at: 2022-12-18 07:46:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2593#issuecomment-1356723252  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2593?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2593](https://codecov.io/gh/boostorg/beast/pull/2593?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6d634d6) into [develop](https://codecov.io/gh/boostorg/beast/commit/5c48142df88d2fd5eb7a225f6cef5665814ee192?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5c48142) will **decrease** coverage by `0.00%`.  
> The diff coverage is `75.53%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2593/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2593?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2593      +/-   ##  
===========================================  
- Coverage    94.70%   94.70%   -0.01%       
===========================================  
  Files          154      154                
  Lines        12027    12039      +12       
===========================================  
+ Hits         11390    11401      +11       
- Misses         637      638       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2593?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2593/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.77% <62.50%> (ø)` | |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2593/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.94% <62.50%> (ø)` | |  
| [include/boost/beast/http/detail/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/2593/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaXBw) | `88.73% <74.41%> (+0.34%)` | :arrow_up: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/2593/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `94.61% <82.35%> (ø)` | |  
| [include/boost/beast/http/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/2593/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `92.18% <100.00%> (+0.04%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2593/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.06% <0.00%> (-0.85%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2593?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2593?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5c48142...6d634d6](https://codecov.io/gh/boostorg/beast/pull/2593?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2022-12-20 03:20:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2593#issuecomment-1358786625  

Clang 5 fails the 17 build because of an incomplete stdlib:  
  
```cpp  
./boost/asio/detail/type_traits.hpp:94:37: error: unknown template name 'invoke_result'  
struct result_of<F(Args...)> : std::invoke_result<F, Args...> {};  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2022-12-20 03:28:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2593#issuecomment-1358790630  

Reader after #2596

---
