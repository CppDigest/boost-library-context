# #1345 Initial implementation of `find_non_centrality` for `non_central_f_distribution` [Merged]

> Username: JacobHass8  
> Created at: 2025-12-28 18:38:39 UTC  
> Updated at: 2026-02-18 16:14:09 UTC  
> Merged at: 2026-02-18 16:13:41 UTC  
> Closed at: 2026-02-18 16:13:41 UTC  
> Url: https://github.com/boostorg/math/pull/1345  

See #1305 for for details.

---

## Comment 1

> Username: JacobHass8  
> Created_at: 2025-12-28 18:39:49 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3694951680  

@dschmitz89 how does this first commit look in regards to https://github.com/boostorg/math/pull/1338#issuecomment-3675187807

---

## Comment 2

> Username: dschmitz89  
> Created_at: 2025-12-29 10:38:07 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3696125820  

Thanks @JacobHass8  for looking into this so quickly. This is a good start. I am not a boost maintainer (barely a boost developer), so can only comment from a high level.  
  
The most important missing bit here is tests for the new functionality. I could not find the current noncentral F distribution tests after 2 minutes though, so we will have to rely on the boost devs to guide us a little here.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-12-30 18:46:21 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3700167977  

Yup, tests needed.  
  
Non central F is treated as a special case of the non-central beta (which is where most of the tests are).  Non-central F tests are fairly lightweight, just there to make sure we have the transformation to non-central beta correct really: https://github.com/boostorg/math/blob/develop/test/test_nc_f.cpp.  A similar level of sanity checking would probably suffice for this function I would guess?

---

## Comment 4

> Username: JacobHass8  
> Created_at: 2026-01-02 20:08:13 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3706146666  

> Yup, tests needed.  
>   
> Non central F is treated as a special case of the non-central beta (which is where most of the tests are). Non-central F tests are fairly lightweight, just there to make sure we have the transformation to non-central beta correct really: https://github.com/boostorg/math/blob/develop/test/test_nc_f.cpp. A similar level of sanity checking would probably suffice for this function I would guess?  
  
I've added a couple of tests and documentation. The tests and documentation copy what is done for the `non_central_chi_squared` distribution. Is there anything else I need to do before this can be merged?

---

## Comment 5

> Username: JacobHass8  
> Created_at: 2026-01-09 21:25:57 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3730621464  

@jzmaddock could I get a review on this when you get the chance? Are there any test cases that you can think of that I should add?

---

## Comment 6

> Username: JacobHass8  
> Created_at: 2026-01-20 22:53:43 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3775329823  

@mborland ([#1305](https://github.com/boostorg/math/issues/1305#issuecomment-3252271409)) if you're interested, here's my first attempt at the inverse of the non-central F distribution. It was pretty much copied from the scipy implementation.

---

## Review 7 [Commented]

> Username: mborland  
> Created_at: 2026-01-21 14:48:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1345#pullrequestreview-3687649635  

This looks pretty good to me. I made a couple minor changes to get it running with CUDA. Since scipy already has an implementation, it's probably worth copying over any tests they have as well.

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2026-01-21 16:02:02 UTC  
> Updated_at: 2026-02-18 16:14:09 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3779166731  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1345?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.33%. Comparing base ([`c42193d`](https://app.codecov.io/gh/boostorg/math/commit/c42193db07aef6f69a439815a9310729282587c7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`4b6975b`](https://app.codecov.io/gh/boostorg/math/commit/4b6975b932661c159911016bb2ae0b6c736df25b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 24 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1345/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1345?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1345   +/-   ##  
========================================  
  Coverage    95.32%   95.33%             
========================================  
  Files          825      825             
  Lines        67994    68061   +67       
========================================  
+ Hits         64815    64883   +68       
+ Misses        3179     3178    -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1345?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/non\_central\_f.hpp](https://app.codecov.io/gh/boostorg/math/pull/1345?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_f.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfZi5ocHA=) | `87.96% <100.00%> (+2.46%)` | :arrow_up: |  
| [test/test\_nc\_f.cpp](https://app.codecov.io/gh/boostorg/math/pull/1345?src=pr&el=tree&filepath=test%2Ftest_nc_f.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X25jX2YuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1345/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1345?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1345?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c42193d...4b6975b](https://app.codecov.io/gh/boostorg/math/pull/1345?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 9

> Username: dschmitz89  
> Created_at: 2026-01-21 20:52:39 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3781112828  

> This looks pretty good to me. I made a couple minor changes to get it running with CUDA. Since scipy already has an implementation, it's probably worth copying over any tests they have as well.  
  
SciPy does not have tests for these functions at the moment yet. The functions were simply incorporated a long time ago with the cdflib library but actually never unit tested. One more plus point for the migration we are planning.

---

## Comment 10

> Username: JacobHass8  
> Created_at: 2026-01-21 21:04:00 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3781154587  

> SciPy does not have tests for these functions at the moment yet. The functions were simply incorporated a long time ago with the cdflib library but actually never unit tested. One more plus point for the migration we are planning.  
  
There's actually a couple [here](https://github.com/scipy/scipy/blob/f0ffa719b6a98aacb5f9bed67f41701076bfcee4/scipy/special/tests/test_basic.py#L742). It's only really 3 spot checks.   
  
I could add some edge case testing like what happens if $p=0,1$ or $nc = \infty$. I could also do more spot checks for small and large values of $nc$ if that would help.

---

## Comment 11

> Username: dschmitz89  
> Created_at: 2026-01-21 21:20:47 UTC  
> Updated_at: 2026-01-21 21:24:30 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3781221636  

> > SciPy does not have tests for these functions at the moment yet. The functions were simply incorporated a long time ago with the cdflib library but actually never unit tested. One more plus point for the migration we are planning.  
>   
> There's actually a couple [here](https://github.com/scipy/scipy/blob/f0ffa719b6a98aacb5f9bed67f41701076bfcee4/scipy/special/tests/test_basic.py#L742). It's only really 3 spot checks.  
>   
> I could add some edge case testing like what happens if p = 0 , 1 or n c = ∞ . I could also do more spot checks for small and large values of n c if that would help.  
  
Ah, thanks for spotting those. The file where the function is supposed to be tested, lists it as [untested](https://github.com/scipy/scipy/blob/f0ffa719b6a98aacb5f9bed67f41701076bfcee4/scipy/special/tests/test_cdflib.py#L8).   
  
About tests here: as long as boost maintainers are happy, it should be good. In SciPy, we do not need to test the function very extensively if it is already thoroughly tested in boost (although one never knows in future, SciPy has demanding users .. ).

---

## Comment 12

> Username: mborland  
> Created_at: 2026-01-21 21:33:45 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3781265837  

> I could add some edge case testing like what happens if p = 0 , 1 or n c = ∞ . I could also do more spot checks for small and large values of n c if that would help.  
  
I think adding all of these makes sense. As @dschmitz89 alluded to, the SciPy users are pretty adept at finding weakness in our edge cases when they exist.

---

## Comment 13

> Username: JacobHass8  
> Created_at: 2026-01-22 02:04:04 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3782095823  

All the tests are passing except for g++-14 c++23 autodiff which appears to be unrelated to this PR. The error is below.   
  
<details><summary>Details</summary>  
<p>  
  
```bash  
unknown location(0): fatal error: in "test_autodiff_8/heuman_lambda_hpp<_Float32>": Throw location unknown (consider using BOOST_THROW_EXCEPTION)  
Dynamic exception type: boost::wrapexcept<std::domain_error>  
std::exception::what: Error in function boost::math::heuman_lambda<N5boost4math15differentiation11autodiff_v16detail4fvarIDF32_Lm5EEE>(N5boost4math15differentiation11autodiff_v16detail4fvarIDF32_Lm5EEE, N5boost4math15differentiation11autodiff_v16detail4fvarIDF32_Lm5EEE): When 1-k^2 == 1 then phi must be < Pi/2, but got phi = depth(1)(4.20396852,1,0,0,0,0)  
  
test_autodiff_8.cpp(38): last checkpoint  
  
*** 1 failure is detected in the test module "test_autodiff"  
```  
  
</p>  
</details>

---

## Comment 14

> Username: JacobHass8  
> Created_at: 2026-01-22 03:31:31 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3782336929  

> I think adding all of these makes sense. As @dschmitz89 alluded to, the SciPy users are pretty adept at finding weakness in our edge cases when they exist.  
  
I've added tests to make sure that $p=0, 1$ and $q=0,1$ throw errors. The spot tests already included have large values of `nc`. I had to do some special handling when $nc = 0$ and the non-central distribution reduces to the F-distribution. This is done in the following lines   
```C++  
fisher_f_distribution<RealType, Policy> dist(v1, v2);   
if (boost::math::relative_difference(pdf(dist, x), p) < 1e-7){  
   return 0;  
}  
```  
I first tried a relative difference of `boost::math::tools::epsilon<RealType>()`, but this only worked for `long doubles`. I've added a number of spot checks for when $nc=0$, and this condition works in those cases.

---

## Comment 15

> Username: mborland  
> Created_at: 2026-01-22 14:03:10 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3784588880  

> > I think adding all of these makes sense. As @dschmitz89 alluded to, the SciPy users are pretty adept at finding weakness in our edge cases when they exist.  
>   
> I've added tests to make sure that p = 0 , 1 and q = 0 , 1 throw errors. The spot tests already included have large values of `nc`. I had to do some special handling when n c = 0 and the non-central distribution reduces to the F-distribution. This is done in the following lines  
>   
> ```c++  
> fisher_f_distribution<RealType, Policy> dist(v1, v2);   
> if (boost::math::relative_difference(pdf(dist, x), p) < 1e-7){  
>    return 0;  
> }  
> ```  
>   
> I first tried a relative difference of `boost::math::tools::epsilon<RealType>()`, but this only worked for `long doubles`. I've added a number of spot checks for when n c = 0 , and this condition works in those cases.  
  
There is also `boost::math::epsilon_difference` which would allow you to set a certain eps threshold. That might give more consistency across type since the relative distance tolerance should be dependent on precision of the type. Then you could have something like:  
  
```c++  
fisher_f_distribution<RealType, Policy> dist(v1, v2);   
if (boost::math::epsilon_difference(pdf(dist, x), p) <= 1){  
    return 0;  
}  
```  
  
`float_distance` could also work but I believe it's more expensive to calculate:  
  
```c++  
fisher_f_distribution<RealType, Policy> dist(v1, v2);   
if (abs(boost::math::float_distance(pdf(dist, x), p)) <= 1){  
    return 0;  
}  
```  
  
See also: https://www.boost.org/doc/libs/latest/libs/math/doc/html/math_toolkit/float_comparison.html

---

## Comment 16

> Username: JacobHass8  
> Created_at: 2026-01-22 17:05:57 UTC  
> Updated_at: 2026-01-22 17:09:52 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3785551787  

> There is also `boost::math::epsilon_difference` which would allow you to set a certain eps threshold. That might give more consistency across type since the relative distance tolerance should be dependent on precision of the type. Then you could have something like:  
>   
> ```c++  
> fisher_f_distribution<RealType, Policy> dist(v1, v2);   
> if (boost::math::epsilon_difference(pdf(dist, x), p) <= 1){  
>     return 0;  
> }  
> ```  
>   
> `float_distance` could also work but I believe it's more expensive to calculate:  
>   
> ```c++  
> fisher_f_distribution<RealType, Policy> dist(v1, v2);   
> if (abs(boost::math::float_distance(pdf(dist, x), p)) <= 1){  
>     return 0;  
> }  
> ```  
  
Hmmm, neither of these conditions worked either. For `floats`, `abs(boost::math::float_distance(pdf(dist, x), p))` and `boost::math::epsilon_difference(pdf(dist, x), p) ` were both of order 1e8.  
  
I also tried   
```C++  
tools::eps_tolerance<RealType> tol(policies::digits<RealType, Policy>());  
fisher_f_distribution<RealType, Policy> dist(v1, v2);   
if (tol(pdf(dist, x), p)){  
    return 0;  
}  
```  
and that didn't work either. The relative difference decreases with increased precision though.

---

## Comment 17

> Username: JacobHass8  
> Created_at: 2026-01-22 19:42:05 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3786345451  

I know what is going wrong now. When calculating the `pdf` of `dist` for floats:  
```c++  
fisher_f_distribution<RealType, Policy> dist(v1, v2);   
if (abs(boost::math::float_distance(pdf(dist, x), p)) <= 1){  
    return 0;  
}  
```  
the template `RealType` is upcast to a `long double` (maybe a `double`, I'm not 100% sure). If I change the `dist` variable above to `non_central_f_distribution<float, policies::policy<> > dist(v1, v2, 0);`, and take the relative distance I get 0 as expected. I also think we should be comparing to `non_central_f_distribution` with `nc=0` rather than `fisher_f_distribution` because they calculate the pdf differently.   
  
I'm not sure if there is an elegant way to avoid this upcasting based on type because this happens before `find_non_centrality_f` is called.

---

## Comment 18

> Username: mborland  
> Created_at: 2026-01-22 20:02:21 UTC  
> Updated_at: 2026-01-22 20:02:37 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3786432262  

> I know what is going wrong now. When calculating the `pdf` of `dist` for floats:  
>   
> ```c++  
> fisher_f_distribution<RealType, Policy> dist(v1, v2);   
> if (abs(boost::math::float_distance(pdf(dist, x), p)) <= 1){  
>     return 0;  
> }  
> ```  
>   
> the template `RealType` is upcast to a `long double` (maybe a `double`, I'm not 100% sure). If I change the `dist` variable above to `non_central_f_distribution<float, policies::policy<> > dist(v1, v2, 0);`, and take the relative distance I get 0 as expected. I also think we should be comparing to `non_central_f_distribution` with `nc=0` rather than `fisher_f_distribution` because they calculate the pdf differently.  
>   
> I'm not sure if there is an elegant way to avoid this upcasting based on type because this happens before `find_non_centrality_f` is called.  
  
Instead of using `policies::policy<>` as your template parameter to `non_central_f_distribution` you should be able to use `policies::policy<policies::promote_float<false>>` in order to avoid this.

---

## Comment 19

> Username: JacobHass8  
> Created_at: 2026-01-22 20:37:09 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3786584677  

> Instead of using `policies::policy<>` as your template parameter to `non_central_f_distribution` you should be able to use `policies::policy<policies::promote_float<false>>` in order to avoid this.  
  
Not quite, because `RealType` at this point has already been upcast to a `long double` from a `float`. If we just stick with a relative precision less than 1e-7, then I don't think we'll have to deal with all the up/down casting based on type/policy.

---

## Comment 20

> Username: mborland  
> Created_at: 2026-01-22 20:52:23 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3786637165  

> > Instead of using `policies::policy<>` as your template parameter to `non_central_f_distribution` you should be able to use `policies::policy<policies::promote_float<false>>` in order to avoid this.  
>   
> Not quite, because `RealType` at this point has already been upcast to a `long double` from a `float`. If we just stick with a relative precision less than 1e-7, then I don't think we'll have to deal with all the up/down casting based on type/policy.  
  
Ah, you're correct. @jzmaddock do you have any final comments for this one before merge?

---

## Review 21 [Commented]

> Username: jzmaddock  
> Created_at: 2026-01-24 13:47:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1345#pullrequestreview-3701629249  

📁 include/boost/math/distributions/non_central_f.hpp

```diff
  61 |+             // long doubles would correctly return 0.
  62 |+             fisher_f_distribution<RealType, Policy> dist(v1, v2); 
  63 |+             if (boost::math::relative_difference(pdf(dist, x), p) < 1e-7){
```

> Username: jzmaddock  
> Created_at: 2026-01-24 13:47:48 UTC  
> Updated_at: 2026-01-24 13:47:49 UTC  
> Url: https://github.com/boostorg/math/pull/1345#discussion_r2724174716  

This looks suspicious to me, some possibly naive questions:  
  
1) Should we not be checking CDF and not PDF here?  
2) Do we want to find non-centralities with very small values, ie below 1e-7?  
  
One possible alternative would be to call `f(tools::min_value<T>())` and if the answer lies to the left of that, then return 0.  Hopefully bracket_and_solve_root will handle all answers to the right of `min_value()`.  Hope that makes sense!


---

## Comment 22

> Username: jzmaddock  
> Created_at: 2026-01-27 13:08:54 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3805136139  

I don't want to push this in case I'm missing something (quite likely!), but this seems to work, and allows us to find very small non-centralities:  
  
```  
diff --git a/include/boost/math/distributions/non_central_f.hpp b/include/boost/math/distributions/non_central_f.hpp  
index 02a75a28d..c6b189e9d 100644  
--- a/include/boost/math/distributions/non_central_f.hpp  
+++ b/include/boost/math/distributions/non_central_f.hpp  
@@ -46,7 +46,7 @@ namespace boost  
          };  
            
          template <class RealType, class Policy>  
-         BOOST_MATH_GPU_ENABLED RealType find_non_centrality_f(const RealType x, const RealType v1, const RealType v2, const RealType p, const RealType q, const Policy& pol)  
+         BOOST_MATH_GPU_ENABLED RealType find_non_centrality_f(const RealType x, const RealType v1, const RealType v2, const RealType p, const RealType q, const RealType p_q_precision, const Policy& pol)  
          {  
             constexpr auto function = "non_central_f<%1%>::find_non_centrality";  
   
@@ -55,16 +55,19 @@ namespace boost  
                   RealType(boost::math::numeric_limits<RealType>::quiet_NaN()), Policy()); // LCOV_EXCL_LINE  
             }  
   
+            non_centrality_finder_f<RealType, Policy> f(x, v1, v2, p < q ? p : q, p < q ? false : true);  
             // Check if nc = 0 (which is just the F-distribution)  
-            // I first tried comparing to boost::math::tools::epsilon<RealType>(),  
-            // but this was never satisfied for floats or doubles. Only   
-            // long doubles would correctly return 0.  
-            fisher_f_distribution<RealType, Policy> dist(v1, v2);   
-            if (boost::math::relative_difference(pdf(dist, x), p) < 1e-7){  
+            // This occurs when the root finder would need to find a result smaller than  
+            // tools::min_value (which it cannot do).  Note that we have to add in a small  
+            // amount of "tolerance" since the subtraction in our termination condition  
+            // implies a small amount of wobble in the result which should be of the  
+            // order p * eps (note not q * eps, since q is calculated as 1-p).  
+            // Also note that p_q_precision is passed down from our caller as the  
+            // epsilon of the original called values, and not after possible promotion.  
+            if (f(tools::min_value<RealType>()) <= 3 * p_q_precision * p){  
                return 0;  
             }  
   
-            non_centrality_finder_f<RealType, Policy> f(x, v1, v2, p < q ? p : q, p < q ? false : true);  
             RealType guess = RealType(10);                       // Starting guess.  
             RealType factor = RealType(2);                                 // How big steps to take when searching.  
             boost::math::uintmax_t max_iter = policies::get_max_root_iterations<Policy>();  
@@ -134,6 +137,7 @@ namespace boost  
                static_cast<eval_type>(v2),  
                static_cast<eval_type>(p),  
                static_cast<eval_type>(1-p),  
+               static_cast<eval_type>(tools::epsilon<RealType>()),  
                forwarding_policy());  
             return policies::checked_narrowing_cast<RealType, forwarding_policy>(  
                result,  
@@ -156,6 +160,7 @@ namespace boost  
                static_cast<eval_type>(c.param2),  
                static_cast<eval_type>(1-c.param3),  
                static_cast<eval_type>(c.param3),  
+               static_cast<eval_type>(tools::epsilon<RealType>()),  
                forwarding_policy());  
             return policies::checked_narrowing_cast<RealType, forwarding_policy>(  
                result,  
diff --git a/test/test_nc_f.cpp b/test/test_nc_f.cpp  
index 3ccb9e3b4..d337f588b 100644  
--- a/test/test_nc_f.cpp  
+++ b/test/test_nc_f.cpp  
@@ -337,7 +337,7 @@ void test_spots(RealType)  
    boost::math::non_central_f_distribution<RealType> dist_no_centrality(a, b, nc);  
    for (RealType x : x_vals)  
    {  
-      RealType P = pdf(dist_no_centrality, x);  
+      RealType P = cdf(dist_no_centrality, x);  
       BOOST_CHECK_CLOSE(dist.find_non_centrality(x, a, b, P), static_cast<RealType>(0), tolerance);  
    }  
    // Case when P=1 or P=0   
```

---

## Comment 23

> Username: JacobHass8  
> Created_at: 2026-01-27 20:14:40 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3807326453  

> I don't want to push this in case I'm missing something (quite likely!), but this seems to work, and allows us to find very small non-centralities:  
  
If I understand your suggestions, this is exactly what I was hoping to do. My understanding is that we're testing if the relative error of the input cdf, `p`, and the cdf calculated with non-centraility `tools::min_value<RealType>()` is less than `3 * tools::epsilon<RealType>()`.   
  
I'm seeing that a couple tests are failing, but once I've figured those out I will push.

---

## Comment 24

> Username: JacobHass8  
> Created_at: 2026-01-27 20:58:16 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3807503626  

> I don't want to push this in case I'm missing something (quite likely!), but this seems to work, and allows us to find very small non-centralities:  
  
I went ahead and accepted your changes! This test was still not returning 0 as it should   
```C++  
    typedef boost::math::concepts::real_concept RealType;  
    RealType a = 5;  
    RealType b = 2;  
    RealType nc = 0;  
    RealType x_vals[] = { 0.25 };  
    boost::math::non_central_f_distribution<RealType> dist_no_centrality(a, b, nc);  
    for (RealType x : x_vals)  
    {  
        RealType P = cdf(dist_no_centrality, x);  
        std::cout << boost::math::non_central_f_distribution<RealType>::find_non_centrality(x, a, b, P) << std::endl; // 6.60778e-19  
    }  
```  
However, since the non-centrality was well within the type's tolerance, I modified the tests to check if the non-centrality was less than the tolerance  
```C++  
BOOST_CHECK(dist.find_non_centrality(x, a, b, P) < tolerance);  
```  
as opposed where I was previously checking if the value was exactly 0:   
```C++  
BOOST_CHECK_CLOSE(dist.find_non_centrality(x, a, b, P), 0, tolerance);  
```

---

## Comment 25

> Username: JacobHass8  
> Created_at: 2026-01-27 21:04:40 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3807528678  

Would retaining information about the original input type be something that should be delegated to the policy? That way the precision of the original type could be accessed even after the values have been promoted. This would allow us to get rid of the `p_q_precision` parameter. I was surprised this wasn't already a feature of policies, but I'm not very familiar with them. Hopefully, this request makes sense.

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2026-01-28 12:56:02 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3811154490  

>If I understand your suggestions, this is exactly what I was hoping to do. My understanding is that we're testing if the relative error of the input cdf, p, and the cdf calculated with non-centraility tools::min_value<RealType>() is less than 3 * tools::epsilon<RealType>().  
  
In theory, we shouldn't need a tolerance test here, if the returned value from the functor is < 0 then the result is smaller than tools::min_value(), ie 0.  However, the functor has a subtraction as it's last step, so there is inevitably an error of p * eps.  
  
> I'm seeing that a couple tests are failing, but once I've figured those out I will push.  
  
That's annoying, it's passing for me with msvc, will take a look at GCC.  
  
> BOOST_CHECK(dist.find_non_centrality(x, a, b, P) < tolerance);  
  
I'm not sure if that's quite right, since presumably we want to be able to accurately find non-centralities that are very small (much smaller than machine epsilon).  Do you know which of the x values was failing?  
  
> Would retaining information about the original input type be something that should be delegated to the policy? That way the precision of the original type could be accessed even after the values have been promoted. This would allow us to get rid of the p_q_precision parameter. I was surprised this wasn't already a feature of policies, but I'm not very familiar with them. Hopefully, this request makes sense.  
  
Well the simple answer is this is the first time we've needed this ;)  We could retain the original pre-promoted type in the policy, but it would rather complicate their construction.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2026-01-28 14:12:02 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3811545816  

Update: the original tests (checking for zero result) work for me with gcc cygwin and mingw.  What was the combination that was failing for you?

---

## Comment 28

> Username: JacobHass8  
> Created_at: 2026-01-28 17:44:52 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3812835090  

> Update: the original tests (checking for zero result) work for me with gcc cygwin and mingw. What was the combination that was failing for you?  
  
Huh, weird. Here's the code example that is failing for me.   
```C++  
typedef boost::math::concepts::real_concept RealType;  
  
RealType a = 5;  
RealType b = 2;  
RealType nc = 0;  
RealType x_vals[] = { 0.25 };  
boost::math::non_central_f_distribution<RealType> dist_no_centrality(a, b, nc);  
for (RealType x : x_vals)  
{  
    RealType P = cdf(dist_no_centrality, x);  
    std::cout << boost::math::non_central_f_distribution<RealType>::find_non_centrality(x, a, b, P) << std::endl; // 6.60778e-19  
    }  
```  
Note, that `f(tools::min_value<RealType>())` is returning something of order `1e-11`. I'm running Ubuntu and g++.

---

## Comment 29

> Username: jzmaddock  
> Created_at: 2026-01-30 12:29:29 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3823521555  

I get negative values from f() for that case here, but I suspect the error rates from real_concept are higher and that's messing things up.  I'm trying a tighter test case to see what happens.

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2026-01-30 16:36:29 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3824636838  

Weird, the ubuntu failures seem to be c++20 only.

---

## Comment 31

> Username: JacobHass8  
> Created_at: 2026-01-30 17:06:31 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3824761637  

> Weird, the ubuntu failures seem to be c++20 only.  
  
That is weird! The reported non-centrality of `6.60778e-19` is what I'm seeing too.

---

## Comment 32

> Username: mborland  
> Created_at: 2026-01-30 17:37:54 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3824876864  

Interestingly the errors from GCC and Clang 16-18 are all using the same standard library version: `GNU libstdc++ version 20240908`. I tried an older version and get the same error:  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Tests run with GNU C++ version 9.5.0, GNU libstdc++ version 20220527, linux  
Testing spot values with type float (Tolerance = 1.19209e-05%).  
Testing spot values with type double (Tolerance = 2.22045e-14%).  
Testing spot values with type long double (Tolerance = 1.0842e-17%).  
Testing spot values with type real_concept (Tolerance = 1.0842e-17%).  
test_nc_f.cpp(339): error: in "test_main": check dist.find_non_centrality(x, a, b, P) <= boost::math::tools::min_value<RealType>() * 10 has failed [6.60778e-19 > 3.3621e-4931]  
  
*** 1 failure is detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
====== END OUTPUT ======```

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2026-01-31 12:45:42 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3828411800  

It's weird, and doesn't seem to be related to this PR as such, but it is telling us something, and it would be good to get to the bottom of it.

---

## Comment 34

> Username: JacobHass8  
> Created_at: 2026-02-09 22:13:22 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3874147052  

> Interestingly the errors from GCC and Clang 16-18 are all using the same standard library version: `GNU libstdc++ version 20240908`.   
  
I think I'm running version 20240904. I haven't been able to test the issue because I haven't been able to switch versions.

---

## Comment 35

> Username: jzmaddock  
> Created_at: 2026-02-10 16:14:30 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3878990816  

I had a chance to look into this: the function we are finding the root for, is basically zero for any non-centrality below about 10^-8 and bobbles about a few epsilon either side of zero "all the way down" as it were.  I think @JacobHass8 had it right the first time (sorry!) and we should check for low absolute error when non-centrality is at or near zero.  Sorry, I've not had a chance to report back before now.

---

## Comment 36

> Username: JacobHass8  
> Created_at: 2026-02-10 18:15:56 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3879907231  

> I had a chance to look into this: the function we are finding the root for, is basically zero for any non-centrality below about 10^-8 and bobbles about a few epsilon either side of zero "all the way down" as it were. I think @JacobHass8 had it right the first time (sorry!) and we should check for low absolute error when non-centrality is at or near zero. Sorry, I've not had a chance to report back before now.  
  
Weird! I thought your solution was going to work. I still find it strange that the tests only failed for older versions of libstdc++. I'll revert to using the absolute error but leave a note about your findings if anyone wants to investigate in the future.

---

## Comment 37

> Username: JacobHass8  
> Created_at: 2026-02-11 04:20:07 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3882036453  

Going back to the relative error seems to fix most of the bugs. Two systems are failing though because the root finding fails. I think this is in the case when the nc=0.

---

## Comment 38

> Username: jzmaddock  
> Created_at: 2026-02-11 16:06:09 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3885407968  

Ah, I didn't explain that very well... I meant to keep the header as I had it - I think conceptually that's the correct thing to do, and the hard coded 1e-6 you have there still looks suspicious to me - and change the _tests_ back to checking for _absolute_ error when zero is the expected result.  That way if there exists a degree of freedom for which small non-centrality has an effect, then we're still covered.

---

## Comment 39

> Username: JacobHass8  
> Created_at: 2026-02-12 18:32:00 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3892690649  

> Ah, I didn't explain that very well... I meant to keep the header as I had it - I think conceptually that's the correct thing to do, and the hard coded 1e-6 you have there still looks suspicious to me - and change the _tests_ back to checking for _absolute_ error when zero is the expected result. That way if there exists a degree of freedom for which small non-centrality has an effect, then we're still covered.  
  
Oops, I see what you meant now. I'll revert it back to the header you had and change the tests. Sorry for the miscommunication! Hopefully this fixes everything.

---

## Comment 40

> Username: JacobHass8  
> Created_at: 2026-02-12 19:20:32 UTC  
> Updated_at: 2026-02-12 20:03:29 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3892925444  

Okay, I reverted the header file and changed the tests to check that the non-centrality is within tolerance with the expected result is 0. Every test past except for cygwin which failed during installation for, presumably, an unrelated reason.

---

## Comment 41

> Username: jzmaddock  
> Created_at: 2026-02-13 16:38:34 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3898153484  

Thanks @JacobHass8 for persisting with this one.  Other than the include of `boost/math/special_functions/relative_difference.hpp` possibly no longer being needed it looks like this is good to go?

---

## Comment 42

> Username: JacobHass8  
> Created_at: 2026-02-13 18:44:58 UTC  
> Url: https://github.com/boostorg/math/pull/1345#issuecomment-3898774820  

> Thanks @JacobHass8 for persisting with this one. Other than the include of `boost/math/special_functions/relative_difference.hpp` possibly no longer being needed it looks like this is good to go?  
  
Thanks for all your guidance on this! I think it's ready to go. I removed the includes `boost/math/special_functions/relative_difference.hpp` and `boost/math/distributions/fisher_f.hpp` which I was using in early stages of this PR.

---
