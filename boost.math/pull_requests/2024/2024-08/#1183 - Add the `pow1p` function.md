# #1183 Add the `pow1p` function [Open]

> Username: mborland  
> Created at: 2024-08-19 18:13:43 UTC  
> Updated at: 2026-01-05 17:59:20 UTC  
> Url: https://github.com/boostorg/math/pull/1183  

This implements the `pow1p` function as discussed and implemented in https://github.com/scipy/scipy/issues/21404 and https://github.com/scipy/scipy/pull/21410. Simplifies a couple of the branches and adds in policy based design for the error paths. This version is also tested with `real_concept` and on the GPU platforms (NVCC, NVRTC, and SYCL).   
  
@fancidev let me know if you see any errors in simplification. The one to call out is here: https://github.com/boostorg/math/compare/pow1p?expand=1#diff-1a6b6446fd424370a1fe6e67b8166e41ab4805c1ecfc4ca15ceae7492f4023a5R137. I fall back to `pow` instead of throwing on non-integer power.  
  
@NAThompson and @jzmaddock  your review would be appreciated as always.  
  
CC: @WarrenWeckesser

---

## Comment 1

> Username: fancidev  
> Created_at: 2024-08-19 22:46:18 UTC  
> Updated_at: 2024-08-19 22:51:03 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-2297639178  

It’s really cool to have this in boost! Thanks a ton!  
  
There is some refinement I’m looking to apply: (1) represent the result of $s/t$ using double-double, and (2) expand $\ln(1+u)$ to second order. I think with these refinements   
the implementation is (more or less) provably accurate to a few machine epsilon. The current code still has some input that leads to relative error over `1e-14`, which I was able to construct by an error analysis.  
  
I’m going to work out the proof on paper first, and then suggest the changes here.  
  
Another optimization that could be applied is that if a floating point type is available whose mantissa has more bits than the total bits of `T`, such as double vs float or x87 long double vs double, then it should be accurate enough (for `T`) to compute $(1+x)^y$ using `exp(y*log1p(x))`, provided `*`, `log1p`, and `exp` are accurately computed using the extended precision type. I’lol try verify this on paper and through numerical experiments.  
  
Finally, I’m going to prepare some edge input as test cases, to complement your randomized test cases, which I find really helpful!  
  
P.S. The (floating point) exception signaling (via the policy object) might have some corner cases to refine, but I’d leave that after the computational part is finalized.  
  
Thanks again for bring this into boost!!

---

## Comment 2

> Username: fancidev  
> Created_at: 2024-08-25 02:56:35 UTC  
> Updated_at: 2024-08-25 02:58:36 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-2308632770  

I made an update to the algorithm (in the referenced PR) that makes the evaluation more accurate for tiny $x$ and huge $y$:  
  
https://github.com/scipy/scipy/pull/21410/commits/a3f89c50a20a6f5c1506b8e2c0756a499190d667  
  
I think the method is now accurate to a few epsilon relative error for all $x > -1$ and all $y$, and I expect minimal changes to this part in the future. A bit more update may come for the case $x < -1$. I'm still in the progress of adding more test cases and completing the algorithm description.  
  
@mborland  If you could update line 193-219 of `pow1p.hpp` with the changes it would be great! Otherwise I'll suggest a patch off your PR after I finish the remaining points.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-08-26 13:59:20 UTC  
> Updated_at: 2024-08-26 15:11:29 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-2310289083  

> I made an update to the algorithm (in the referenced PR) that makes the evaluation more accurate for tiny x and huge y :  
>   
> [scipy/scipy@a3f89c5](https://github.com/scipy/scipy/commit/a3f89c50a20a6f5c1506b8e2c0756a499190d667)  
>   
> I think the method is now accurate to a few epsilon relative error for all x > − 1 and all y , and I expect minimal changes to this part in the future. A bit more update may come for the case x < − 1 . I'm still in the progress of adding more test cases and completing the algorithm description.  
>   
> @mborland If you could update line 193-219 of `pow1p.hpp` with the changes it would be great! Otherwise I'll suggest a patch off your PR after I finish the remaining points.  
  
Changes have been applied.  
  
Edit: @fancidev as you work on test cases it looks like right now I am not hitting anything in your double-T range with the random values. If you could come up with cases to exercise those it would be helpful.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-08-26 15:07:20 UTC  
> Updated_at: 2025-12-29 20:06:22 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-2310444299  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1183?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `92.07921%` with `8 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 94.06%. Comparing base ([`66b362a`](https://app.codecov.io/gh/boostorg/math/commit/66b362aec58b252f179546a0a77fdb6541f1469d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`f7150ee`](https://app.codecov.io/gh/boostorg/math/commit/f7150ee9765eec76cedc9e8d867458743a1bf715?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 539 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1183?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/pow1p.hpp](https://app.codecov.io/gh/boostorg/math/pull/1183?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fpow1p.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3BvdzFwLmhwcA==) | 87.30% | [8 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1183/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1183      +/-   ##  
===========================================  
- Coverage    94.08%   94.06%   -0.03%       
===========================================  
  Files          780      782       +2       
  Lines        65815    65916     +101       
===========================================  
+ Hits         61922    62003      +81       
- Misses        3893     3913      +20       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1183?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_pow1p.cpp](https://app.codecov.io/gh/boostorg/math/pull/1183?src=pr&el=tree&filepath=test%2Ftest_pow1p.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3BvdzFwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/pow1p.hpp](https://app.codecov.io/gh/boostorg/math/pull/1183?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fpow1p.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3BvdzFwLmhwcA==) | `87.30% <87.30%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1183/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1183?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1183?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [66b362a...f7150ee](https://app.codecov.io/gh/boostorg/math/pull/1183?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 5

> Username: JacobHass8  
> Created_at: 2025-12-29 21:46:47 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-3697609091  

> Edit: @fancidev as you work on test cases it looks like right now I am not hitting anything in your double-T range with the random values. If you could come up with cases to exercise those it would be helpful.  
  
I found a simple test that hits the double-T range code. Let $x >> 1$ and $y = 1/x$ and then compute `boost::math::pow1p(x,y)` which is approximately $e$. Letting $y = z/x$ for some $z > 0$ we should get $e^z$. Here's a quick example:  
```C  
// Test double arithmetic  
const auto x = static_cast<T>(1e-25);  
const auto y = 1 / x;  
CHECK_ULP_CLOSE(boost::math::pow1p(x, y), T(M_E), 1000);  
```  
I'm not sure if the ULP distance being 1000 is acceptable. For this example the ULP distance is 667 (for types `long double` and `boost::math::concepts::real_concept`). Perhaps Taylor expanding to higher order would improve this?    
  
Additionally, I let $x$ be positive with $x << 1$ and $y = 1/x$ again. Mathematica is saying that $(1+x)^y \approx 1$ but the current implementation of `pow1p` is returning $e$. In this case we are not hitting the double-T range code.

---

## Comment 6

> Username: fancidev  
> Created_at: 2026-01-04 02:26:13 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-3707547301  

Thanks for checking this out, @JacobHass8.  
  
> I'm not sure if the ULP distance being 1000 is acceptable. For this example the ULP distance is 667 (for types `long double` and `boost::math::concepts::real_concept`). Perhaps Taylor expanding to higher order would improve this?  
  
The ULP should be in the single digit ideally. A likely cause of the loss of accuracy is that the compiler aggressively optimises the "fast_two_sum" construction such that it is no longer exact. In follow-up commits to the original scipy PR, I spent quite a bit of time to hack the [fast_two_sum](https://github.com/scipy/scipy/pull/21410/files) function with various preprocessor macros to try to avoid premature optimisation, but I can't say it works reliably for every compiler/system combination. The lack of a reliable fast_two_sum is a reason I failed to make progress on pushing the PR to production.  
  
A second reason is that I did not get 100% confident about the error analysis, and even if I did, the analysis seems a bit overly complicated to verify. (I wouldn't like to repeat the error analysis for the function $(1+x)^y-1$, which I find useful in a few places too.) The reason for going through this complex route was to make use of the system's `pow`, `exp` and `log` functions. But there are reasons against it too: Firstly, the accuracy of system's `pow` function varies quite a bit across systems, making it hard to provide a guarantee of the accuracy of `pow1p`. Secondly, a lot of the (tedious) special case handling are actually duplicated between `pow1p` and `pow`. And lastly but importantly, it seems common implementations of `pow` already does the exp-log trick to double-double precision, so it would seem more efficient at runtime to build upon that route, which would also simply the error analysis a lot.  
  
> Additionally, I let x be positive with x << 1 and y = 1 / x again. Mathematica is saying that ( 1 + x ) y ≈ 1 but the current implementation of `pow1p` is returning e . In this case we are not hitting the double-T range code.  
  
If I'm not mistaken, $\lim_{x \rightarrow 0} (1+x)^{1/x} = \lim_{n \rightarrow \infty} (1+1/n)^n = e$.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2026-01-04 08:55:07 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-3707881775  

>> I'm not sure if the ULP distance being 1000 is acceptable.  
  
> The ULP should be in the single digit ideally. A likely cause of the loss of accuracy is that the compiler aggressively optimises the `fast_two_sum` construction such that it is no longer exact.  
  
Writing any kind of portable `fast_two_sum` is challenging in C/C++. Basically, you need to guarantee a precise floating-point model so that sums and differences, even if minute, remain sums and differences. In some compiler jargon, this would mean something like you can't use `-ffast-math` on GCC/clang or you need `/fp:precise` on MSVC. But these constraints don't work in Math since we can't ask clients to do any of these settings.  
  
You _might_ try making sure that the operands of sums and differences in `fast_two_sum` have the `volatile` quality. Then just empirically find out if it passes all of CI with the tolerances needed. CI picks up the compilers that are known-supported. And if any compiler is found later that fails, it could be treated as a special case.

---

## Comment 8

> Username: JacobHass8  
> Created_at: 2026-01-04 17:28:57 UTC  
> Updated_at: 2026-01-05 17:59:20 UTC  
> Url: https://github.com/boostorg/math/pull/1183#issuecomment-3708272799  

@fancidev and @ckormanyos I screwed up with the testing so I think the precision is okay when x gets small and y gets big. Here's what I should have done  
```C++  
typedef long double T;  
const auto x = static_cast<T>(1e-25);  
const auto y = 1 / x;  
auto trueVal = 2.7182818284590452353602873354385710748049853256855984661255565104L;  
std::cout << boost::math::float_distance(boost::math::pow1p(x,y), trueVal) << std::endl; // 0  
```  
I previously used $e$ as the true value that I was comparing pow1p to. However, this is only an approximation. When I plugged this example into Mathematica and used that as the true value, I got a ULP of 0. So sorry for the scare.   
  
> If I'm not mistaken, lim x → 0 ( 1 + x ) 1 / x = lim n → ∞ ( 1 + 1 / n ) n = e .  
  
Yes, you're right. My example wasn't right. It should have been $\lim_{x\rightarrow \infty} (1+x)^{1/x}=1$. On the other hand, we have $\lim_{x\rightarrow 0} (1+x)^{1/x}=e$. Here's a code snippet which shows that result.   
```C++  
typedef long double T;  
const auto x = static_cast<T>(1e25);  
const auto y = 1 / x;  
std::cout << boost::math::float_distance(boost::math::pow1p(x,y), 1.0000000000000000000000057564627324851142100449952151425057639965L) << std::endl; // 0  
```  
I think everything is okay then? I was getting confused with the different limits. Sorry for all the mistakes!

---
