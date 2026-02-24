# #684 Handle issue652 with upper-bound check [Merged]

> Username: ckormanyos  
> Created at: 2025-04-29 08:18:17 UTC  
> Updated at: 2025-04-29 15:32:00 UTC  
> Merged at: 2025-04-29 15:31:22 UTC  
> Closed at: 2025-04-29 15:31:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/684  

This is intended to repair #652, which I believe is an actual bug. The upper-bound check was missing on the unsigned version of the conversion routine (it was already present for the signed case).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2025-04-29 08:31:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/684#issuecomment-2837934616  

Thanks Chris, yes your fix looks correct to me!

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-04-29 08:42:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/684#issuecomment-2837963489  

>  yes your fix looks correct to me!  
  
John, I was pondering this fix. You know, I did not (yet) explicitly or purposely handle the case of negative `cpp_bin_float` when converting to `unsigned long long`. Do you know what should happen for this conversion? I could program the behavior now in this PR if it's incorrect.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-04-29 09:02:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/684#issuecomment-2838013922  

Ugh, always another corner case!  I guess since we saturate, then zero would be the logical choice?  
  
If they aren't there already, we should have some consistent tests (by which I mean that are run for all the integer backends) for all this.  I can't remember off the top of my head where I put conversion tests (for saturation especially) - whether they are in test_arithmetic.hpp with everything else, or in the separate conversion testers.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-04-29 09:04:21 UTC  
> Updated_at: 2025-04-29 15:31:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/684#issuecomment-2838019476  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`c433245`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/c433245e392ffbd79aeb896f6000a6f032835d8d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e3b50cf`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/e3b50cfc76d4f35b9c00bdb1efd295920ebfab35?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/684/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #684     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          279     280      +1       
  Lines        28983   29067     +84       
=========================================  
+ Hits         27257   27342     +85       
+ Misses        1726    1725      -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_bin\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0LmhwcA==) | `89.6% <100.0%> (-0.1%)` | :arrow_down: |  
| [include/boost/multiprecision/cpp\_dec\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_dec_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGVjX2Zsb2F0LmhwcA==) | `94.1% <100.0%> (+0.1%)` | :arrow_up: |  
| [test/git\_issue\_652.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?src=pr&el=tree&filepath=test%2Fgit_issue_652.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNjUyLmNwcA==) | `100.0% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/684/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c433245...e3b50cf](https://app.codecov.io/gh/boostorg/multiprecision/pull/684?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-04-29 09:16:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/684#issuecomment-2838054567  

> Ugh, always another corner case! I guess since we saturate, then zero would be the logical choice?  
  
Indeed! OK, I'm working on it....  
  
I just ran some tests on `cpp_dec_float`, `cpp_bin_float` and built-in `double`, such as [here at godbolt](https://godbolt.org/z/YE7PfWqrr).  
  
I will make bin-float behave the same as dec-float and `double`. I'll go ahead and pack the code and its tests right in this PR while we are on such a roll.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-04-29 13:05:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/684#issuecomment-2838802818  

Hi John (@jzmaddock) so now `cpp_dec_float` and `cpp_bin_float` behave closer to how they should and seemingly identically with each other regarding overflow/underflow of integral conversions. I added the relevant tests to the Jamfile and they are **all cycling green now**.  
  
I am purposely **not looking into** `gmp_float` and `mpfr_float` in this particular matter at this time. But I've made a note to take a cursory glance at their behaviors. I'll also keep this and these tests in mind if we ever get back  to `cpp_double_fp_backend`.

---
