# #2919 Performance improvement [Closed]

> Username: BrianWeed  
> Created at: 2024-08-14 17:34:26 UTC  
> Updated at: 2024-08-15 07:20:21 UTC  
> Closed at: 2024-08-15 07:20:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2919  

By-value sink params can be moved into their final destination

---

## Comment 1

> Username: ashtum  
> Created_at: 2024-08-14 17:39:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2919#issuecomment-2289430956  

Hi, `std::uint64_t` is a trivial type, so moving it is not more efficient than copying it.

---

## Comment 2

> Username: BrianWeed  
> Created_at: 2024-08-14 18:17:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2919#issuecomment-2289522621  

Ah, yes, I forgot that optional uses inline storage and not dynamically allocated memory.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-08-14 21:25:21 UTC  
> Updated_at: 2024-08-15 07:20:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2919#issuecomment-2289932950  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2919?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.07%. Comparing base [(`848e206`)](https://app.codecov.io/gh/boostorg/beast/commit/848e20680fb374532a0c5816b79b38532e6da775?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`29b1a88`)](https://app.codecov.io/gh/boostorg/beast/commit/29b1a883e2dd3ab3d3232ec3ff5c0089ed3bd5ff?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2919/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2919?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2919   +/-   ##  
========================================  
  Coverage    93.07%   93.07%             
========================================  
  Files          177      177             
  Lines        13709    13709             
========================================  
  Hits         12759    12759             
  Misses         950      950             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2919?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2919?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `94.11% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2919?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2919?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [848e206...29b1a88](https://app.codecov.io/gh/boostorg/beast/pull/2919?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
