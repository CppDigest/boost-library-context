# #239 Remove superflous test duplication [Merged]

> Username: Flamefire  
> Created at: 2024-12-27 18:27:52 UTC  
> Updated at: 2025-01-03 18:59:45 UTC  
> Merged at: 2025-01-03 18:19:10 UTC  
> Closed at: 2025-01-03 18:19:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/239  

The template argument in the detail::parser tests is not used so the exact same test is executed multiple times.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-27 19:17:32 UTC  
> Updated_at: 2025-01-03 18:24:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/239#issuecomment-2563966517  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/239?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.75%. Comparing base [(`8fbdb8a`)](https://app.codecov.io/gh/boostorg/charconv/commit/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8951db1`)](https://app.codecov.io/gh/boostorg/charconv/commit/8951db1b3eb54354ea49385aaf4673ede42428c5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 30 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/239/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/239?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #239      +/-   ##  
===========================================  
- Coverage    94.76%   94.75%   -0.01%       
===========================================  
  Files           69       69                
  Lines         9031     9023       -8       
===========================================  
- Hits          8558     8550       -8       
  Misses         473      473                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/239?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_parser.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/239?src=pr&el=tree&filepath=test%2Ftest_parser.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3BhcnNlci5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/239?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/239?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8fbdb8a...8951db1](https://app.codecov.io/gh/boostorg/charconv/pull/239?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
