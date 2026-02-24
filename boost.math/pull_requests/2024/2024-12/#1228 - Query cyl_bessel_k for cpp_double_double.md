# #1228 Query cyl_bessel_k for cpp_double_double [Merged]

> Username: ckormanyos  
> Created at: 2024-12-22 13:50:47 UTC  
> Updated at: 2024-12-24 11:31:06 UTC  
> Merged at: 2024-12-24 11:30:52 UTC  
> Closed at: 2024-12-24 11:30:52 UTC  
> Url: https://github.com/boostorg/math/pull/1228  

Hi John (@jzmaddock) I'm down to only 2 remaining functions having troubles with the candidate `cpp_double_double`.  
  
On `cyl_bessel_k(-1000, T(700.0))` I have issues that I can't resolve alone. The result is not bad, having $O(10^{-31}$, but the intermediate steps do some underflowing.  
  
This calculation breaks the `min_exponent10`/`max_exponent10` near-symmetry, so the proposed (big) change in `precision.hpp` has motivated by `cpp_double_double` having `min_exponent10`/`max_exponent10` of $-291/+308$.  
  
The real problem, however came when starting the recursion for $K_{1000}(700)$. The value of $K_0(700)$ came back subnormal and the value of $K_1(0)$ was zero. This messed up the recursion start and lead to the wrong answer, regardless of how `exp(-x)` was calculated (halved or not).  
  
I know there would theoreticlaly be a way to do the recursion with another scaling, but that would be a very large change in Math requiring start points for scaled/unscaled $K_0(x)$ and $K_1(x)$.  
  
Do you see a way that the unusual `cpp_double_double` can actually with modest impact on Math calculate $K_{1000}(700)$?  
  
Cc: @mborland

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-22 14:50:13 UTC  
> Updated_at: 2024-12-22 15:33:51 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2558481323  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1228?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.83%. Comparing base [(`52b029f`)](https://app.codecov.io/gh/boostorg/math/commit/52b029ffa1b6b5b4e44419e17c9e50c2d5a6fac5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2a7bef3`)](https://app.codecov.io/gh/boostorg/math/commit/2a7bef3410c5b359e8c148d3c0739737185f2c49?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1228/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1228?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1228      +/-   ##  
===========================================  
- Coverage    93.83%   93.83%   -0.01%       
===========================================  
  Files          657      657                
  Lines        55243    55231      -12       
===========================================  
- Hits         51838    51826      -12       
  Misses        3405     3405                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1228?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/tools/precision.hpp](https://app.codecov.io/gh/boostorg/math/pull/1228?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fprecision.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3ByZWNpc2lvbi5ocHA=) | `95.74% <ø> (-0.87%)` | :arrow_down: |  
| [test/test\_bessel\_k.hpp](https://app.codecov.io/gh/boostorg/math/pull/1228?src=pr&el=tree&filepath=test%2Ftest_bessel_k.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9rLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1228?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1228?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [52b029f...2a7bef3](https://app.codecov.io/gh/boostorg/math/pull/1228?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-12-23 13:28:47 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2559712985  

>The real problem, however came when starting the recursion for K 1000 ( 700 ) . The value of K 0 ( 700 ) came back subnormal and the value of K 1 ( 0 ) was zero. This messed up the recursion start and lead to the wrong answer, regardless of how exp(-x) was calculated (halved or not).  
  
>I know there would theoreticlaly be a way to do the recursion with another scaling, but that would be a very large change in Math requiring start points for scaled/unscaled K 0 ( x ) and K 1 ( x ) .  
  
>Do you see a way that the unusual cpp_double_double can actually with modest impact on Math calculate K 1000 ( 700 ) ?  
  
  
This is a Math lib bug, and I've filed an issue with reduced test case here: https://github.com/boostorg/math/issues/1229.  
  
It's not trivial to fix though, and I'm not sure how important the use case is, so my gut feeling is to push this to the long grass for the minute.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-12-23 13:53:05 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2559750685  

> It's not trivial to fix though, and I'm not sure how important the use case is, so my gut feeling is to push this to the long grass for the minute.  
  
Thank you John. That seems like a wise idea since scaling the recursion or other heavy-handed activities seem like a bit much.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-12-23 17:51:53 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2560096427  

Hi John,  
  
I thought it might be easiest to keep the test point (the arrays and all the indexing stay the same), but change the order and argument. The test case appears in three individual areas.  
  
I'm liking  
  
$$  
K_{-129}\left(200\right)~{\approx}~3.61744436315860678558682169223740584132967454950379795115566^{-71}{\text{,}}  
$$  
  
for this test case, with $K_{0}(200)$ and $K_{1}(200)$ having $O(-88)$, and only about 2-3 decimal digits of cancellation.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-12-23 17:52:53 UTC  
> Updated_at: 2024-12-23 17:53:07 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2560097474  

If you get a chance, John (@jzmaddock), no hurry though, I'd like your opinion on my proposed changes to `precision.hpp` in this PR also.  
  
Cc: @mborland

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-12-23 18:17:04 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2560124097  

Remember to also compute the derivative.  
  
Note to self:  
  
```  
N[D[BesselK[-129, x], x] /. {x -> 200}, 62]  
```  
  
$$  
{\approx}-4.3110345255133348027545113739271337415489367194240537230455182^{-71}  
$$

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2024-12-24 11:25:21 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2561014404  

Hi @ckormanyos and Merry Christmas!  Yes those changes are all good: if you wanted to be extra picky about it, you could add full specializations of log_max_value/log_min_value (in your MP code) given that those could presumably be noexcept and constexpr then?  But yes, please do go ahead and merge this PR when ready!

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-12-24 11:31:05 UTC  
> Url: https://github.com/boostorg/math/pull/1228#issuecomment-2561019512  

Thanks John (@jzmaddock) Merry Christmas also! Nothing like a few special functions of applied mathematics to raise the holida spirit!  
  
> Yes those changes are all good:  
  
Great!  
  
> if you wanted to be extra picky about it, you could add full specializations of log_max_value/log_min_value (in your MP code) given that those could presumably be noexcept and constexpr then?  
  
I was considering adding these within the code of the multi-precision backend. Your comment now completes my motivation to do this. I'll do it in the next round. It is too time consuming to compute the logarithms in actual run-time, so a constexpr pure number would, in fact, be superior.  
  
> But yes, please do go ahead and merge this PR when ready!  
  
DONE!

---
