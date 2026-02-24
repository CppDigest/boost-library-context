# #35 Fix deprecated copy warnings [Merged]

> Username: apolukhin  
> Created at: 2022-01-13 10:48:42 UTC  
> Updated at: 2024-11-29 17:27:09 UTC  
> Merged at: 2024-11-29 17:26:31 UTC  
> Closed at: 2024-11-29 17:26:31 UTC  
> Url: https://github.com/boostorg/concept_check/pull/35  

All the implicit special member functions made explicit in this PR.  
It fixes the compilation and test runs with `./b2 libs/concept_check/test/ cxxstd=2a,03,11,17,14 "cxxflags=-Werror=deprecated-copy" toolset=clang-13 -j4`

---

## Comment 1

> Username: jeking3  
> Created_at: 2022-04-24 21:46:36 UTC  
> Url: https://github.com/boostorg/concept_check/pull/35#issuecomment-1107924383  

@apolukhin could you rebase on develop to get a fresh CI run?  CI is passing.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-05-19 11:46:14 UTC  
> Updated_at: 2024-11-29 17:27:09 UTC  
> Url: https://github.com/boostorg/concept_check/pull/35#issuecomment-1131586773  

## [Codecov](https://app.codecov.io/gh/boostorg/concept_check/pull/35?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 100.00%. Comparing base [(`5656ffe`)](https://app.codecov.io/gh/boostorg/concept_check/commit/5656ffe0ac76ad58fbaff45baff20c39924a1278?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9552501`)](https://app.codecov.io/gh/boostorg/concept_check/commit/9552501bb2d95a90684a91d9d3e63bfc789a317f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 11 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/concept_check/pull/35/graphs/tree.svg?width=650&height=150&src=pr&token=4FWH598KON&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/concept_check/pull/35?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop       #35   +/-   ##  
=========================================  
  Coverage   100.00%   100.00%             
=========================================  
  Files            1         1             
  Lines            3         3             
=========================================  
  Hits             3         3             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/concept_check/pull/35?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/concept_check/pull/35?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5656ffe...9552501](https://app.codecov.io/gh/boostorg/concept_check/pull/35?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: apolukhin  
> Created_at: 2022-05-19 11:59:22 UTC  
> Url: https://github.com/boostorg/concept_check/pull/35#issuecomment-1131598239  

@jeking3 done, CI is green

---

## Comment 4

> Username: apolukhin  
> Created_at: 2022-06-05 11:51:53 UTC  
> Url: https://github.com/boostorg/concept_check/pull/35#issuecomment-1146790551  

@jeking3 gentle reminder

---

## Comment 5

> Username: apolukhin  
> Created_at: 2022-09-02 16:11:45 UTC  
> Url: https://github.com/boostorg/concept_check/pull/35#issuecomment-1235679890  

@jeking3 gentle reminder

---
