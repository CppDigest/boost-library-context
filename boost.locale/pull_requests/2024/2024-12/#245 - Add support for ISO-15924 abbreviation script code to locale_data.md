# #245 Add support for ISO-15924 abbreviation script code to locale_data [Merged]

> Username: Flamefire  
> Created at: 2024-12-01 17:31:05 UTC  
> Updated at: 2024-12-03 11:19:29 UTC  
> Merged at: 2024-12-03 11:19:04 UTC  
> Closed at: 2024-12-03 11:19:04 UTC  
> Url: https://github.com/boostorg/locale/pull/245  

The value is (currently) ignored but this allows to parse locale names returned by ICU.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-03 10:37:40 UTC  
> Updated_at: 2024-12-03 11:19:29 UTC  
> Url: https://github.com/boostorg/locale/pull/245#issuecomment-2514173197  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/245?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.84%. Comparing base [(`a996b00`)](https://app.codecov.io/gh/boostorg/locale/commit/a996b00c46ad83d36f5d0557071e52a0452873a2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1d85ae3`)](https://app.codecov.io/gh/boostorg/locale/commit/1d85ae3162a13bd8e8e9b3ca87423dd76a009a87?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/245/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/245?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #245      +/-   ##  
===========================================  
+ Coverage    95.81%   95.84%   +0.02%       
===========================================  
  Files          117      117                
  Lines        10352    10410      +58       
===========================================  
+ Hits          9919     9977      +58       
  Misses         433      433                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/245?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/util/locale\_data.hpp](https://app.codecov.io/gh/boostorg/locale/pull/245?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Futil%2Flocale_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC9sb2NhbGVfZGF0YS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/util/locale\_data.cpp](https://app.codecov.io/gh/boostorg/locale/pull/245?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Futil%2Flocale_data.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2xvY2FsZV9kYXRhLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_util.cpp](https://app.codecov.io/gh/boostorg/locale/pull/245?src=pr&el=tree&filepath=test%2Ftest_util.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0aWwuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/245?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/245?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a996b00...1d85ae3](https://app.codecov.io/gh/boostorg/locale/pull/245?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
