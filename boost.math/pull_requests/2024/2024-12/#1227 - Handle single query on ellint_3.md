# #1227 Handle single query on ellint_3 [Closed]

> Username: ckormanyos  
> Created at: 2024-12-20 18:19:09 UTC  
> Updated at: 2025-05-04 08:50:40 UTC  
> Closed at: 2024-12-21 12:11:13 UTC  
> Url: https://github.com/boostorg/math/pull/1227  

Hi John (@jzmaddock),  
  
So I'm boiling down the _double-double_ backend. I was happily able to remove a few errors based on your advice so far.  
  
I've come down to a small handful of queries. We need to go through these one at a time. I am a little bit shaky in this area. But I'll just express the query anyway.  
  
In this PR, we find one single change on `ellint_3` test data. I suspect this change is needed because we call `ellint_3` with an infinite-parameter whereby there is no check for infinity other than a `throw`.  
  
I see two choices here:  
  
- Add some more refined check-code to `ellint_3`  
- or avoid the test case for situations not able to handle the large order of the argument.  
  
Your thoughts?  
  
Cc: @mborland

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-20 18:58:11 UTC  
> Updated_at: 2024-12-21 12:11:20 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2557555828  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1227?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.71%. Comparing base [(`52b029f`)](https://app.codecov.io/gh/boostorg/math/commit/52b029ffa1b6b5b4e44419e17c9e50c2d5a6fac5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`abc73f6`)](https://app.codecov.io/gh/boostorg/math/commit/abc73f65c52441b6ef76b7616560caf4bd67ff84?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1227/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1227?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1227      +/-   ##  
===========================================  
- Coverage    93.83%   93.71%   -0.13%       
===========================================  
  Files          657      644      -13       
  Lines        55243    54138    -1105       
===========================================  
- Hits         51838    50736    -1102       
+ Misses        3405     3402       -3       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1227?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_ellint\_3.hpp](https://app.codecov.io/gh/boostorg/math/pull/1227?src=pr&el=tree&filepath=test%2Ftest_ellint_3.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VsbGludF8zLmhwcA==) | `100.00% <ø> (ø)` | |  
  
... and [35 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1227/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1227?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1227?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [52b029f...abc73f6](https://app.codecov.io/gh/boostorg/math/pull/1227?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-12-21 09:50:07 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558068487  

I might need to look at this some more: the `LDBL_MAX_EXP > 16382` check is there purely so we don't get compiler errors from an out-of-range literal when `long double=double`.  Clearly this "works" when `T=double` and `long double` is an extended type, so it should work for `double double` too...

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-12-21 10:18:11 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558074774  

OK, so we're calculating `ellint_3(0, -INF,, 0.0009765625)`, and simulating your type approximately with cpp_bin_float:  
  
```  
using dd_t = boost::multiprecision::number<boost::multiprecision::debug_adaptor<boost::multiprecision::cpp_bin_float<106, boost::multiprecision::digit_base_2, void, int, std::numeric_limits<double>::min_exponent, std::numeric_limits<double>::max_exponent>>>;  
```  
  
I get the correct answer of 0.  
  
It all comes down to: https://github.com/boostorg/math/blob/52b029ffa1b6b5b4e44419e17c9e50c2d5a6fac5/include/boost/math/special_functions/ellint_3.hpp#L145  
  
which evaluates as `atan(INF)/INF` which should be zero.  
  
Where do you see things go wrong and/or throw for your type?

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-12-21 11:14:16 UTC  
> Updated_at: 2024-12-21 11:14:54 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558086556  

> Where do you see things go wrong and/or throw for your type?  
  
I'm working frantically on that.  
  
> It all comes down to: [line 145]  
  
Um, I do not reach line 145. I return from the subroutine at line 71 with a domain error.  
  
The check at line 68 tells me that `(v > 0)` is true since `v` is $\infty$. And the second logic check on line 68 `(1 / v < (sphi * sphi))` also ends up being true for me since $1/{\infty}$ is zero and `sphi` is real and finite.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-12-21 11:19:05 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558087523  

> The check at line 68  
  
In fact, I need to find out how the similarly configured `cpp_bin_float` type squeezes through that logic?. Or is it I, myself, as usual, making a blunder of assumption here?

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-12-21 11:33:28 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558090565  

Thinking a bit more, how can you get to line 145 if `v` is $\infty$? Shouldnt the logic check at line 142 `(v < 1)` be false if `v` is $\infty$?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2024-12-21 11:43:13 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558092496  

I have:  
  
sphi = 0.000976562344779578298906910168119791  
k = 0  
v = -INF  
  
So... the check at line 58 fails because `k * k * sphi * sphi` is zero.  
  
And the check at line 68 also fails because `v > 0` is false for v = -INF.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-12-21 11:51:35 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558094108  

> And the check at line 68 also fails because `v > 0` is false for v = -INF.  
  
Ah. My infinity is POSITIVE (as in +INF). I must have a critical error somewhere in the backend. I will find this now. Thank you John.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2024-12-21 12:09:46 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558098535  

John (@jzmaddock) many thanks. The _specfun_ tests are really good!  
  
This test case revealed two critical errors in the preliminary `cpp_double_fp_backend`!  
  
- We were not retaining the negative sign for overly-large string read.  
- And another error was revealed! The backend had $\sqrt{\infty}={\text{NaN}}$, instead of $\infty$.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2024-12-21 12:11:13 UTC  
> Updated_at: 2024-12-21 12:11:30 UTC  
> Url: https://github.com/boostorg/math/pull/1227#issuecomment-2558098925  

Clesed PR as error in client multiple-precision backend candidate. Thanks John AGAIN! (@jzmaddock).  
  
Cc: @mborland

---
