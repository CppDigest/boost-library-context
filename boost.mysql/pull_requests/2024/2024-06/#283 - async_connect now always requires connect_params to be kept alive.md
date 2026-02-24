# #283 async_connect now always requires connect_params to be kept alive [Merged]

> Username: anarthal  
> Created at: 2024-06-19 10:26:31 UTC  
> Updated at: 2024-06-20 10:04:14 UTC  
> Merged at: 2024-06-20 10:04:13 UTC  
> Closed at: 2024-06-20 10:04:13 UTC  
> Url: https://github.com/boostorg/mysql/pull/283  

This trims down complexity and build times.  
asio::ssl::context is now forward-declared in any_connection.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-19 17:04:48 UTC  
> Updated_at: 2024-06-20 04:51:30 UTC  
> Url: https://github.com/boostorg/mysql/pull/283#issuecomment-2179168387  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/283?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `94.11765%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 98.46%. Comparing base [(`08abb5d`)](https://app.codecov.io/gh/boostorg/mysql/commit/08abb5d073524967d07e78cbd5449f4a6e64e6f9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`82ade6d`)](https://app.codecov.io/gh/boostorg/mysql/commit/82ade6de54092cffcd88be8fbc5ec7d4fcb62eb2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/283/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/283?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #283      +/-   ##  
===========================================  
- Coverage    98.50%   98.46%   -0.04%       
===========================================  
  Files          136      135       -1       
  Lines         6537     6516      -21       
===========================================  
- Hits          6439     6416      -23       
- Misses          98      100       +2       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/283?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/283?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `98.88% <100.00%> (-0.05%)` | :arrow_down: |  
| [...lude/boost/mysql/detail/connect\_params\_helpers.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/283?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnect_params_helpers.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdF9wYXJhbXNfaGVscGVycy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/283?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...clude/boost/mysql/impl/internal/variant\_stream.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/283?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fvariant_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3ZhcmlhbnRfc3RyZWFtLmhwcA==) | `90.32% <100.00%> (+0.10%)` | :arrow_up: |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/283?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `95.38% <90.90%> (-1.20%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/283?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/283?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [08abb5d...82ade6d](https://app.codecov.io/gh/boostorg/mysql/pull/283?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
