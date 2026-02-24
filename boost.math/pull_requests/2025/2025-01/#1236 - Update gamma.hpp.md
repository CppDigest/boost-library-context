# #1236 Update gamma.hpp [Merged]

> Username: kenarab  
> Created at: 2025-01-21 16:25:33 UTC  
> Updated at: 2025-01-21 21:16:07 UTC  
> Merged at: 2025-01-21 21:15:52 UTC  
> Closed at: 2025-01-21 21:15:52 UTC  
> Url: https://github.com/boostorg/math/pull/1236  

Missing name for Lanczos parameter in gamma_imp_final at line 114  
  
It caused an error when compiling at line 124  
  
     std::cout << "tgamma_imp called with " << typeid(z).name() << " " << typeid(l).name() << std::endl;  
   
   
Checked on old code and the parameter has l name

---

## Review 1 [Changes requested]

> Username: mborland  
> Created_at: 2025-01-21 16:50:40 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1236#pullrequestreview-2565201057  

Can you add a `(void)l` somewhere? This will cause unused parameter warnings except when printing is enabled.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-01-21 17:53:13 UTC  
> Updated_at: 2025-01-21 21:16:07 UTC  
> Url: https://github.com/boostorg/math/pull/1236#issuecomment-2605390643  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1236?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.83%. Comparing base [(`024a4fe`)](https://app.codecov.io/gh/boostorg/math/commit/024a4fed706474b9e930e30cc3d40b2a5eee8753?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`41790ee`)](https://app.codecov.io/gh/boostorg/math/commit/41790ee6a168c342e1e9185109ca5500fe665b8d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1236/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1236?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1236   +/-   ##  
========================================  
  Coverage    93.83%   93.83%             
========================================  
  Files          657      657             
  Lines        55244    55244             
========================================  
  Hits         51840    51840             
  Misses        3404     3404             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1236?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1236?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `92.25% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1236?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1236?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [024a4fe...41790ee](https://app.codecov.io/gh/boostorg/math/pull/1236?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: kenarab  
> Created_at: 2025-01-21 18:23:58 UTC  
> Url: https://github.com/boostorg/math/pull/1236#issuecomment-2605449907  

Added   the requested code inside the conditional compilation before debugging code  
  
(void)l;

---

## Comment 4

> Username: mborland  
> Created_at: 2025-01-21 18:41:29 UTC  
> Url: https://github.com/boostorg/math/pull/1236#issuecomment-2605482743  

> Added the requested code inside the conditional compilation before debugging code  
>   
> (void)l;  
  
I added a commit moving it outside of the #if block since thats where it'll cause issues. Sorry if I was not sufficiently clear.

---

## Comment 5

> Username: kenarab  
> Created_at: 2025-01-21 21:09:07 UTC  
> Url: https://github.com/boostorg/math/pull/1236#issuecomment-2605743868  

I'm sorry I'm not familiar with that practice so placed it where as little disruption could cause.

---

## Comment 6

> Username: kenarab  
> Created_at: 2025-01-21 21:09:34 UTC  
> Url: https://github.com/boostorg/math/pull/1236#issuecomment-2605744557  

Next time I see this kind of issue I will do it better

---

## Comment 7

> Username: mborland  
> Created_at: 2025-01-21 21:15:46 UTC  
> Url: https://github.com/boostorg/math/pull/1236#issuecomment-2605755096  

No worries. Thanks for the PR!

---
