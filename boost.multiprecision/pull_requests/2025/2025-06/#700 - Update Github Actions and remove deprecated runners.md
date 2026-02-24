# #700 Update Github Actions and remove deprecated runners [Closed]

> Username: mborland  
> Created at: 2025-06-24 21:37:34 UTC  
> Updated at: 2025-06-26 13:24:02 UTC  
> Closed at: 2025-06-26 12:17:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/700  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-24 22:38:59 UTC  
> Updated_at: 2025-06-26 12:17:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/700#issuecomment-3002072700  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/700?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 90.2%. Comparing base [(`2f87bf2`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/2f87bf2ddf05ce871499d37657063e15bdf1aa0e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`39e26c3`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/39e26c3607dac49982169f43951a9007b6a24965?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/700/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/700?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #700      +/-   ##  
==========================================  
- Coverage     94.1%   90.2%    -3.9%       
==========================================  
  Files          280     171     -109       
  Lines        29074   19205    -9869       
==========================================  
- Hits         27347   17310   -10037       
- Misses        1727    1895     +168       
```  
  
[see 146 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/700/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/700?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/700?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2f87bf2...39e26c3](https://app.codecov.io/gh/boostorg/multiprecision/pull/700?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2025-06-25 12:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/700#issuecomment-3004633270  

Interesting that `specfun_cpp_bin_float` fails at 6 hours with GCC-13. It took under 15 minutes with GCC-12

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-06-26 12:22:26 UTC  
> Updated_at: 2025-06-26 12:22:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/700#issuecomment-3008301355  

> Interesting that specfun_`cpp_bin_float` fails at 6 hours with GCC-13. It took under 15 minutes with GCC-12  
  
I think the word _interesting_ is an understatment. That's more of a no-go. Is it possible in any way, that there might be different optimization settings? Or might the lexical parser actually be that much slower?  
  
Ultimately, we might need to look into this together...

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-06-26 12:23:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/700#issuecomment-3008305193  

I think it ran fast on my branch in #701 in that PR. So maybe this 6 hours here was just a phantom

---

## Comment 5

> Username: mborland  
> Created_at: 2025-06-26 12:27:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/700#issuecomment-3008314863  

I didn't run it enough times to see if GCC-13 had reputable problems. I bumped everything to 14 which performed all the tests in the expected amount of time.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-06-26 13:24:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/700#issuecomment-3008488622  

> I bumped everything to 14 which performed all the tests in the expected amount of time.  
  
Ah I get it. You mean that was an intermediate state. Yeah that is good now.

---
