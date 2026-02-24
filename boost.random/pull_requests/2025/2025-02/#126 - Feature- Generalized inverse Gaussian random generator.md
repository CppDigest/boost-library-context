# #126 Feature: Generalized inverse Gaussian random generator [Merged]

> Username: ygeunkim  
> Created at: 2025-02-02 05:16:00 UTC  
> Updated at: 2025-02-06 13:11:40 UTC  
> Merged at: 2025-02-06 13:11:18 UTC  
> Closed at: 2025-02-06 13:11:18 UTC  
> Url: https://github.com/boostorg/random/pull/126  

From Issue #125, added generalized inverse Gaussian distribution.  
  
- Added `random/generalized_inverse_gaussian_distribution.hpp`  
- Added `test/test_generalized_inverse_gaussian_distribution.cpp`  
- Algorithm used: [Devroye (2014)](https://link.springer.com/article/10.1007/s11222-012-9367-z)  
  
Need to check  
  
- Naming: Name is quite long.  
- Edge case: Whether to consider very small parameter case (can be covered using `gamma_distribution<>`)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-02 05:43:46 UTC  
> Updated_at: 2025-02-06 13:11:40 UTC  
> Url: https://github.com/boostorg/random/pull/126#issuecomment-2629254053  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/126?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.83%. Comparing base [(`8378063`)](https://app.codecov.io/gh/boostorg/random/commit/8378063ac7ff99645a7fb51a4b985e7ec43450a1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`61d50dc`)](https://app.codecov.io/gh/boostorg/random/commit/61d50dcb5157c47af67983c15d95291644744dc0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/126/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/126?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #126      +/-   ##  
===========================================  
+ Coverage    95.77%   95.83%   +0.05%       
===========================================  
  Files          113      114       +1       
  Lines         8000     8115     +115       
===========================================  
+ Hits          7662     7777     +115       
  Misses         338      338                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/126?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ndom/generalized\_inverse\_gaussian\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/126?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fgeneralized_inverse_gaussian_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZ2VuZXJhbGl6ZWRfaW52ZXJzZV9nYXVzc2lhbl9kaXN0cmlidXRpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_distribution.ipp](https://app.codecov.io/gh/boostorg/random/pull/126?src=pr&el=tree&filepath=test%2Ftest_distribution.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Rpc3RyaWJ1dGlvbi5pcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/126?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/126?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8378063...61d50dc](https://app.codecov.io/gh/boostorg/random/pull/126?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2025-02-04 18:58:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/126#pullrequestreview-2593783297  

LGTM. Do you still need to think about the two items listed as need to check in your original PR comment? Is it possible to cook up some additional tests that will cover your two uncovered lines?

---

## Comment 3

> Username: ygeunkim  
> Created_at: 2025-02-04 23:37:27 UTC  
> Url: https://github.com/boostorg/random/pull/126#issuecomment-2635302220  

The algorithm has three cases but the test only covers up to 2 cases.  
https://github.com/boostorg/random/blob/8378063ac7ff99645a7fb51a4b985e7ec43450a1/test/test_distribution.ipp#L209  
I'll add it after adding BOOST_RANDOM_TEST3_* in test_distribution.ipp.

---

## Comment 4

> Username: ygeunkim  
> Created_at: 2025-02-05 02:30:01 UTC  
> Updated_at: 2025-02-05 02:30:28 UTC  
> Url: https://github.com/boostorg/random/pull/126#issuecomment-2635564522  

@mborland I checked codecov result, and it looks like every line is covered. Please review the changes. Since I updated the `test_distribution.ipp`, the merge might require a careful look.

---

## Comment 5

> Username: mborland  
> Created_at: 2025-02-05 14:55:07 UTC  
> Url: https://github.com/boostorg/random/pull/126#issuecomment-2637088899  

So this is what I see from the codecov report this morning. Since these are control paths for `operator()` we should have tests for them. If you need instead of expanding the `test_distribution.ipp` file you can add your own cpp file in tests directory. I find `<boost/core/lightweight_test.hpp>` easier to use: https://www.boost.org/doc/libs/master/libs/core/doc/html/core/lightweight_test.html  
  
![Screenshot 2025-02-05 at 9 52 11 AM](https://github.com/user-attachments/assets/d2b3ff0e-ece1-4ee5-b942-3b8490cd0da8)

---

## Comment 6

> Username: ygeunkim  
> Created_at: 2025-02-06 01:06:40 UTC  
> Updated_at: 2025-02-06 01:08:52 UTC  
> Url: https://github.com/boostorg/random/pull/126#issuecomment-2638376476  

I should have added TEST3 in the test file. I mistakenly used TEST2 twice.  
Also, I'll try your suggestion. Since my commit 1f14d9f26bc554a284ea308009dae460528267e1 has added some lines in `test_distribution.ipp`, it might be better to revert it and try the lightweight_test.

---

## Comment 7

> Username: ygeunkim  
> Created_at: 2025-02-06 01:18:18 UTC  
> Url: https://github.com/boostorg/random/pull/126#issuecomment-2638388107  

Every line is now covered.  
  
<img width="1556" alt="image" src="https://github.com/user-attachments/assets/a0ad38ce-621a-498a-b797-f9d36aea0bfd" />

---

## Review 8 [Approved]

> Username: mborland  
> Created_at: 2025-02-06 13:11:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/random/pull/126#pullrequestreview-2598632935  

This looks good to me now. Thanks you!

---
