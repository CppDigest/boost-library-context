# #3049 replace BOOST_STATIC_ASSERT with BOOST_CORE_STATIC_ASSERT [Merged]

> Username: ashtum  
> Created at: 2025-10-22 14:41:43 UTC  
> Updated at: 2025-10-24 09:55:21 UTC  
> Merged at: 2025-10-24 09:55:21 UTC  
> Closed at: 2025-10-24 09:55:21 UTC  
> Url: https://github.com/boostorg/beast/pull/3049  

Closes #3048

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-22 21:16:41 UTC  
> Updated_at: 2025-10-23 02:37:49 UTC  
> Url: https://github.com/boostorg/beast/pull/3049#issuecomment-3434232662  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3049?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.21%. Comparing base ([`63f3d3f`](https://app.codecov.io/gh/boostorg/beast/commit/63f3d3f3930288726b5862f24728ed9d418d080a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`1b0cc09`](https://app.codecov.io/gh/boostorg/beast/commit/1b0cc0945c089310a45be0af8e09a0ca7c7707b4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3049/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #3049   +/-   ##  
========================================  
  Coverage    93.21%   93.21%             
========================================  
  Files          176      176             
  Lines        13690    13690             
========================================  
  Hits         12761    12761             
  Misses         929      929             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3049?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/varint.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetail%2Fvarint.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC92YXJpbnQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/flat\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fflat_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/buffers\_prefix.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fbuffers_prefix.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19wcmVmaXguaHBw) | `97.11% <ø> (ø)` | |  
| [include/boost/beast/core/rate\_policy.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Frate_policy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3JhdGVfcG9saWN5LmhwcA==) | `53.33% <ø> (ø)` | |  
| [include/boost/beast/http/detail/rfc7230.ipp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fdetail%2Frfc7230.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmlwcA==) | `90.00% <ø> (ø)` | |  
| [include/boost/beast/http/impl/fields.ipp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Ffields.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `89.02% <ø> (ø)` | |  
| [include/boost/beast/zlib/detail/deflate\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3049?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fzlib%2Fdetail%2Fdeflate_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3049?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3049?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [63f3d3f...1b0cc09](https://app.codecov.io/gh/boostorg/beast/pull/3049?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
