# #240 Remove superflous assignment to exponent argument in parser [Merged]

> Username: Flamefire  
> Created at: 2024-12-27 20:08:46 UTC  
> Updated at: 2025-01-02 15:02:37 UTC  
> Merged at: 2025-01-02 13:46:35 UTC  
> Closed at: 2025-01-02 13:46:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/240  

The exponent will be parsed later, the value assigned at this position is meaningless or even wrong.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-27 20:42:44 UTC  
> Updated_at: 2025-01-02 13:46:44 UTC  
> Url: https://github.com/boostorg/charconv/pull/240#issuecomment-2564019599  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/240?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.76%. Comparing base [(`8fbdb8a`)](https://app.codecov.io/gh/boostorg/charconv/commit/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9e4975c`)](https://app.codecov.io/gh/boostorg/charconv/commit/9e4975c94bc50ccc25a633204844fddf1f7bf3b7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/240/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/240?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #240      +/-   ##  
===========================================  
- Coverage    94.76%   94.76%   -0.01%       
===========================================  
  Files           69       69                
  Lines         9031     9030       -1       
===========================================  
- Hits          8558     8557       -1       
  Misses         473      473                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/240?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/parser.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/240?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcGFyc2VyLmhwcA==) | `79.68% <ø> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/240?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/240?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8fbdb8a...9e4975c](https://app.codecov.io/gh/boostorg/charconv/pull/240?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
