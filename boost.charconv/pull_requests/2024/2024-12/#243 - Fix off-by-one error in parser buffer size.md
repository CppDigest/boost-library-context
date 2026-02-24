# #243 Fix off-by-one error in parser buffer size [Merged]

> Username: Flamefire  
> Created at: 2024-12-28 13:21:20 UTC  
> Updated at: 2025-01-02 15:02:57 UTC  
> Merged at: 2025-01-02 13:40:31 UTC  
> Closed at: 2025-01-02 13:40:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/243  

When parsing a value with the maximum number of digits the last digit will be truncated and the exponent increased instead.  
  
This change allows to parse e.g. `<UINT64_MAX>[e0]` correctly.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-28 14:03:54 UTC  
> Updated_at: 2025-01-02 13:40:41 UTC  
> Url: https://github.com/boostorg/charconv/pull/243#issuecomment-2564332770  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/243?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.77%. Comparing base [(`8fbdb8a`)](https://app.codecov.io/gh/boostorg/charconv/commit/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`73eb4f8`)](https://app.codecov.io/gh/boostorg/charconv/commit/73eb4f82920b1977c3fcdeaec27e846fbd6d9023?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/243/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/243?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #243      +/-   ##  
===========================================  
+ Coverage    94.76%   94.77%   +0.01%       
===========================================  
  Files           69       69                
  Lines         9031     9051      +20       
===========================================  
+ Hits          8558     8578      +20       
  Misses         473      473                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/243?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/parser.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/243?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcGFyc2VyLmhwcA==) | `79.79% <100.00%> (ø)` | |  
| [test/test\_parser.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/243?src=pr&el=tree&filepath=test%2Ftest_parser.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3BhcnNlci5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/243?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/243?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8fbdb8a...73eb4f8](https://app.codecov.io/gh/boostorg/charconv/pull/243?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2025-01-02 13:36:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/charconv/pull/243#pullrequestreview-2527582801  

I don't entirely remember why I took off 1 from the buffer size initially, but this change looks good to me. Thanks.

---
