# #1363 Fixes ibeta for large arguments [Open]

> Username: JacobHass8  
> Created at: 2026-02-17 18:47:17 UTC  
> Updated at: 2026-02-23 04:40:51 UTC  
> Url: https://github.com/boostorg/math/pull/1363  

Closes #1361 and https://github.com/scipy/scipy/issues/24566.  
  
Only the expansion for $\eta$ has been added. When to use the erf expansion and relevant tests need to be added.

---

## Comment 1

> Username: JacobHass8  
> Created_at: 2026-02-17 19:45:42 UTC  
> Updated_at: 2026-02-17 19:46:16 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3916747690  

@jzmaddock is there a way to see if the continued fraction is converging quickly or not? Maybe a ratio of subsequent terms or a change in the value of the continued fraction?   
  
In this spirit, the easiest think would just be to add a try/catch statement as done here  
```C++  
try  
{  
   fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative);  
}  
// If series converges slowly, fall back to erf approximation  
catch (boost::math::evaluation_error& e)  
{  
   fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
}  
```  
Maybe this will have unintended consequences though?

---

## Comment 2

> Username: mborland  
> Created_at: 2026-02-17 20:02:13 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3916813249  

> @jzmaddock is there a way to see if the continued fraction is converging quickly or not? Maybe a ratio of subsequent terms or a change in the value of the continued fraction?  
>   
  
Non-programatically you can define `BOOST_MATH_INSTRUMENT` and you'll get all that information dumped into your terminal.  
  
> In this spirit, the easiest think would just be to add a try/catch statement as done here  
>   
> ```c++  
> try  
> {  
>    fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative);  
> }  
> // If series converges slowly, fall back to erf approximation  
> catch (boost::math::evaluation_error& e)  
> {  
>    fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
> }  
> ```  
>   
> Maybe this will have unintended consequences though?  
  
We test and run on a number of `noexcept` as you can probably see from the CI. Right now in a `noexcept` environment it looks like you can't really tell what happened. Perhaps changing the signature to:  
  
```c++  
BOOST_MATH_GPU_ENABLED inline T ibeta_fraction2(T a, T b, T x, T y, const Policy& pol, bool normalised, T* p_derivative, boost::math::uintmax_t iters = nullptr);  
```  
  
Since `max_terms` is written to by reference in the continued fraction impl. Then you could do something a la  
  
```c++  
uintmax_t iters;  
fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative, &iters);  
If (too many iters)  
{  
   fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
}  
```  
  
Defaulting to, and checking the `nullptr` before writing after the throwing location would probably keep this change from affecting other code.

---

## Comment 3

> Username: JacobHass8  
> Created_at: 2026-02-17 20:26:54 UTC  
> Updated_at: 2026-02-17 20:27:59 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3916922142  

> Since `max_terms` is written to by reference in the continued fraction impl. Then you could do something a la  
>   
> ```c++  
> uintmax_t iters;  
> fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative, &iters);  
> If (too many iters)  
> {  
>    fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
> }  
> ```  
>   
> Defaulting to, and checking the `nullptr` before writing after the throwing location would probably keep this change from affecting other code.  
  
The issue is that before returning, `ibeta_fraction2` runs   
```c++  
boost::math::policies::check_series_iterations<T>("boost::math::ibeta", max_terms, pol);  
```  
which throws the evaluation error. I think that your solution would work, but then we would have to get rid of the check above in `ibeta_fraction2` and run it after every call of `ibeta_fraction2`. Does that make sense? I'm not sure if I explained that well.  
  
Maybe there's an easier solution I'm not seeing though. Here's the function in question:   
```C++  
template <class T, class Policy>  
BOOST_MATH_GPU_ENABLED inline T ibeta_fraction2(T a, T b, T x, T y, const Policy& pol, bool normalised, T* p_derivative)  
{  
   typedef typename lanczos::lanczos<T, Policy>::type lanczos_type;  
   BOOST_MATH_STD_USING  
   T result = ibeta_power_terms(a, b, x, y, lanczos_type(), normalised, pol);  
   if(p_derivative)  
   {  
      *p_derivative = result;  
      BOOST_MATH_ASSERT(*p_derivative >= 0);  
   }  
   if(result == 0)  
      return result;  
  
   ibeta_fraction2_t<T> f(a, b, x, y);  
   boost::math::uintmax_t max_terms = boost::math::policies::get_max_series_iterations<Policy>();  
   T fract = boost::math::tools::continued_fraction_b(f, boost::math::policies::get_epsilon<T, Policy>(), max_terms);  
   boost::math::policies::check_series_iterations<T>("boost::math::ibeta", max_terms, pol); // Want to catch this error!   
   BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
   BOOST_MATH_INSTRUMENT_VARIABLE(result);  
   return result / fract;  
}  
```

---

## Comment 4

> Username: mborland  
> Created_at: 2026-02-17 20:43:11 UTC  
> Updated_at: 2026-02-17 20:46:38 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3916994153  

In this case non-convergence isn't exceptional, it's somewhat expected right? Passing a modified policy to `ibeta_fraction2` like `ignore_error` would keep you from throwing at that check. Then this works:  
  
```c++  
template <class T, class Policy>  
BOOST_MATH_GPU_ENABLED inline T ibeta_fraction2(T a, T b, T x, T y, const Policy& pol, bool normalised, T* p_derivative, uintmax_t* iters = nullptr)  
{  
   typedef typename lanczos::lanczos<T, Policy>::type lanczos_type;  
   BOOST_MATH_STD_USING  
   T result = ibeta_power_terms(a, b, x, y, lanczos_type(), normalised, pol);  
   if(p_derivative)  
   {  
      *p_derivative = result;  
      BOOST_MATH_ASSERT(*p_derivative >= 0);  
   }  
   if(result == 0)  
      return result;  
  
   ibeta_fraction2_t<T> f(a, b, x, y);  
   boost::math::uintmax_t max_terms = boost::math::policies::get_max_series_iterations<Policy>();  
   T fract = boost::math::tools::continued_fraction_b(f, boost::math::policies::get_epsilon<T, Policy>(), max_terms);  
   boost::math::policies::check_series_iterations<T>("boost::math::ibeta", max_terms, pol); // Nothing happens  
   if(iters && max_iter >= policies::get_max_series_iterations<Policy>()) *iters = max_iter;  
   BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
   BOOST_MATH_INSTRUMENT_VARIABLE(result);  
   return result / fract;  
}  
```

---

## Comment 5

> Username: JacobHass8  
> Created_at: 2026-02-17 22:04:33 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3917311064  

> In this case non-convergence isn't exceptional, it's somewhat expected right? Passing a modified policy to `ibeta_fraction2` like `ignore_error` would keep you from throwing at that check. Then this works:  
  
I think that should work! I'm not very familiar with policies though. How would I modify the policy to `ignore_error` on within `ibeta_imp`?

---

## Comment 6

> Username: mborland  
> Created_at: 2026-02-17 23:05:50 UTC  
> Updated_at: 2026-02-17 23:06:38 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3917532391  

> > In this case non-convergence isn't exceptional, it's somewhat expected right? Passing a modified policy to `ibeta_fraction2` like `ignore_error` would keep you from throwing at that check. Then this works:  
>   
> I think that should work! I'm not very familiar with policies though. How would I modify the policy to `ignore_error` on within `ibeta_imp`?  
  
```c++  
using ignore_iterations_policy = typename boost::math::policies::normalise<  
        Policy,  
        boost::math::policies::max_series_iterations_error<boost::math::policies::ignore_error>  
    >::type;  
  
    ignore_iterations_policy new_policy;  
```  
  
And then use the `new_policy` object to call `ibeta_fraction2`

---

## Comment 7

> Username: JacobHass8  
> Created_at: 2026-02-18 04:00:13 UTC  
> Updated_at: 2026-02-18 23:25:18 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3918449626  

> ```c++  
> using ignore_iterations_policy = typename boost::math::policies::normalise<  
>         Policy,  
>         boost::math::policies::max_series_iterations_error<boost::math::policies::ignore_error>  
>     >::type;  
>   
>     ignore_iterations_policy new_policy;  
> ```  
  
I implemented this, but am getting a ton of errors when running all the `ibeta` tests. ~~The issue is that there are a number of tests where `iter >= policies::get_max_series_iterations<Policy>()`. In these cases, `boost::math::policies::check_series_iterations<T>("boost::math::ibeta", max_terms, pol)` doesn't throw an error, but the series has converged and the answer should be accepted (at least according to the test data). Just checking `iter >= policies::get_max_series_iterations<Policy>()` will cause the `erf` approximation to be used in these test cases which is less accurate.~~ I think that I had a bug which was causing the number of iterations to be artificially inflated for the tests. I'm finding the number of iterations are actually much more reasonable now.   
  
I'm not sure what the solution here should be. However, the try/catch statement seems the simplest and all of the test cases pass with this implementation.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2026-02-18 11:04:37 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3920168995  

I'll try and look into the policy thing later.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2026-02-18 13:45:30 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3920920012  

It's a bit of a cop-out, but this patch passes tests locally for me:  
  
```  
diff --git a/include/boost/math/special_functions/beta.hpp b/include/boost/math/special_functions/beta.hpp  
index a277c65e8..569a8fb23 100644  
--- a/include/boost/math/special_functions/beta.hpp  
+++ b/include/boost/math/special_functions/beta.hpp  
@@ -1584,46 +1584,39 @@ BOOST_MATH_GPU_ENABLED T ibeta_imp(T a, T b, T x, const Policy& pol, bool inv, b  
       else  
       {  
          // a and b both large:  
-         bool use_asym = false;  
          T ma = BOOST_MATH_GPU_SAFE_MAX(a, b);  
-         T xa = ma == a ? x : y;  
          T saddle = ma / (a + b);  
-         T powers = 0;  
-         if ((ma > 1e-5f / tools::epsilon<T>()) && (ma / BOOST_MATH_GPU_SAFE_MIN(a, b) < (xa < saddle ? 2 : 15)))  
-         {  
-            if (a == b)  
-               use_asym = true;  
-            else  
-            {  
-               powers = exp(log(x / (a / (a + b))) * a + log(y / (b / (a + b))) * b);  
-               if (powers < tools::epsilon<T>())  
-                  use_asym = true;  
-            }  
-         }  
-         if(use_asym)  
+         if ((ma > 1e-5f / tools::epsilon<T>()) && (fabs(saddle - x) < 1e-12) && (1 - saddle > 0.125))  
          {  
             fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
-            if (fract * tools::epsilon<T>() < powers)  
-            {  
-               // Erf approximation failed, correction term is too large, fall back:  
-               fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative);  
-            }  
-            else  
-               invert = false;  
+            invert = false;  
          }  
          else  
          {  
-            try  
+            typedef typename lanczos::lanczos<T, Policy>::type lanczos_type;  
+            T local_result = ibeta_power_terms(a, b, x, y, lanczos_type(), normalised, pol);  
+            if (p_derivative)  
             {  
-               fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative);  
+               *p_derivative = local_result;  
+               BOOST_MATH_ASSERT(*p_derivative >= 0);  
             }  
-            // If series converges slowly, fall back to erf approximation  
-            catch (boost::math::evaluation_error& e)  
+            if (local_result != 0)  
             {  
-               fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
+               ibeta_fraction2_t<T> f(a, b, x, y);  
+               boost::math::uintmax_t max_terms = boost::math::policies::get_max_series_iterations<Policy>();  
+               T local_fract = boost::math::tools::continued_fraction_b(f, boost::math::policies::get_epsilon<T, Policy>(), max_terms);  
+               if (max_terms >= boost::math::policies::get_max_series_iterations<Policy>())  
+               {  
+                  // Continued fraction failed, fall back to asymptotic expansion:  
+                  fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
+                  invert = false;  
+               }  
+               else  
+                  fract = local_result / local_fract;  
             }  
-         }    
-         BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
+            else  
+               fract = 0;  
+         }  
       }  
    }  
    if(p_derivative)  
```

---

## Comment 10

> Username: JacobHass8  
> Created_at: 2026-02-18 23:38:42 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3923810465  

> It's a bit of a cop-out, but this patch passes tests locally for me:  
  
If I'm understanding your code correctly, you've simply just written out all the logic from `ibeta_fraction2` without the series convergence test. Instead, you check if the maximum number of iterations has been reached and use the `ibeta_large_ab` approximation in this case.   
  
This is kind of what I had in mind, but wasn't implementing well. I think this is a reasonable solution! We've copied the code from `ibeta_fraction2`, but this makes more sense to me than defining a new policy.

---

## Comment 11

> Username: JacobHass8  
> Created_at: 2026-02-18 23:55:39 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3923890505  

As far as test data goes, how should I go about testing this region since Mathematica hasn't been able to produce results. The simplest test is to make sure that `ibeta` is always increasing as $x$ increases. I was thinking test that `ibeta` increases in the range $x=\mathrm{saddle} \pm 10^{-10,\ldots, -15}$. This would ensure that we traverse the intersection of the continued fraction and erf approximation.   
  
I could also implement the erf approximation in Mathematica and test that we're close to these values. We wouldn't be comparing to "true" data, but maybe this is the closest that anyone can get?

---

## Comment 12

> Username: codecov[bot]  
> Created_at: 2026-02-19 00:57:25 UTC  
> Updated_at: 2026-02-23 01:57:32 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3924079522  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1363?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `97.29730%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 95.34%. Comparing base ([`c42193d`](https://app.codecov.io/gh/boostorg/math/commit/c42193db07aef6f69a439815a9310729282587c7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`cbf53f8`](https://app.codecov.io/gh/boostorg/math/commit/cbf53f88cce9516b9528cfa5a10b67108d62a606?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 27 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1363?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1363?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | 95.45% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1363?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1363/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1363?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1363      +/-   ##  
===========================================  
+ Coverage    95.32%   95.34%   +0.01%       
===========================================  
  Files          825      825                
  Lines        67994    68177     +183       
===========================================  
+ Hits         64815    65000     +185       
+ Misses        3179     3177       -2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1363?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_ibeta.cpp](https://app.codecov.io/gh/boostorg/math/pull/1363?src=pr&el=tree&filepath=test%2Ftest_ibeta.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2liZXRhLmNwcA==) | `97.82% <100.00%> (ø)` | |  
| [test/test\_ibeta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1363?src=pr&el=tree&filepath=test%2Ftest_ibeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2liZXRhLmhwcA==) | `99.10% <100.00%> (+0.09%)` | :arrow_up: |  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1363?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `99.26% <95.45%> (-0.74%)` | :arrow_down: |  
  
... and [6 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1363/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1363?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1363?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c42193d...cbf53f8](https://app.codecov.io/gh/boostorg/math/pull/1363?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 13

> Username: dschmitz89  
> Created_at: 2026-02-19 08:31:55 UTC  
> Updated_at: 2026-02-19 08:32:29 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3925592242  

Hey everyone,  
just wanted to mention that not every stress test SciPy users come up with warrant a definite "fix" in boost. I do not want to leave boost maintainers with the impression that they get bombarded with edge case handling by us. I also forwarded a few of such issues to you guys in the past but please, do not feel pressurised to respond and fix all of them if they cause more trouble than they are worth.  
  
If you get the feeling that we start playing whack-a-mole in the sense that one fix causes trouble in other cases in this regime, it should be fine if you declare certain inputs infeasible @jzmaddock .

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2026-02-19 16:50:00 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3928482158  

>If I'm understanding your code correctly, you've simply just written out all the logic from ibeta_fraction2 without the series convergence test. Instead, you check if the maximum number of iterations has been reached and use the ibeta_large_ab approximation in this case.  
  
Correct, we can stop a call to ibeta_fraction2 from throwing at this location, but we don't get back the iteration count only the result so far, so a bit of cut and paste seemed the best solution.  
  
>As far as test data goes, how should I go about testing this region since Mathematica hasn't been able to produce results. The simplest test is to make sure that ibeta is always increasing as x increases. I was thinking test that ibeta increases in the range x = saddle ± 10 − 10 , … , − 15 . This would ensure that we traverse the intersection of the continued fraction and erf approximation.  
  
That's sensible, at least we should do a bit of due diligence.  I suspect the error rates are so large in this area that we'll get a lot of noise, and a monotonically increasing result is probably too much to ask for.  What we could do is generate a scatter plot for a handful of values of a and b, and check things look sensible as x goes through the saddle point and we switch methods.  
  
>I could also implement the erf approximation in Mathematica and test that we're close to these values. We wouldn't be comparing to "true" data, but maybe this is the closest that anyone can get?  
  
Hmm, the problem with that is we don't necessarily have a good handle on what the error rate inherent to the approximation is?  
  
>Hey everyone,  
>just wanted to mention that not every stress test SciPy users come up with warrant a definite "fix" in boost. I do not want to leave boost maintainers with the impression that they get bombarded with edge case handling by us. I also forwarded a few of such issues to you guys in the past but please, do not feel pressurised to respond and fix all of them if they cause more trouble than they are worth.  
>  
>If you get the feeling that we start playing whack-a-mole in the sense that one fix causes trouble in other cases in this regime, it should be fine if you declare certain inputs infeasible @jzmaddock .  
  
For sure, and we're definitely on the limit with this one, certainly any results in this domain are likely to be very approximate, but I also think we can do a little bit better than at present.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2026-02-19 17:35:12 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3928769675  

I tried a quick scatter chart (google gemini AI which I don't necessarily trust!!) for a = 10000000272564224, b = 9965820922822656 and distance from saddle point:  
<img width="640" height="480" alt="Code_Generated_Image" src="https://github.com/user-attachments/assets/748f15a9-2111-48af-bec5-5ae1ec77fbbc" />  
  
Looks broadly OK in this case?

---

## Comment 16

> Username: JacobHass8  
> Created_at: 2026-02-19 18:30:57 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3929095796  

> I tried a quick scatter chart (google gemini AI which I don't necessarily trust!!) for a = 10000000272564224, b = 9965820922822656 and distance from saddle point:  
  
Was this made with the files in the current PR? I'd say that looks pretty good too! I can try to write up a test to ensure the values are first off not `nan` and are increasing.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2026-02-19 19:28:28 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3929453176  

>Was this made with the files in the current PR?  
  
This PR with the patch I suggested applied.  It's just one data point of course, but it's encouraging at least.  The values do seem to be monotonically increasing too, though I suspect if you zoomed in on a small enough interval that might change as you see more "noise".  This is probably true of all the special functions though.

---

## Comment 18

> Username: JacobHass8  
> Created_at: 2026-02-19 20:15:35 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3929713100  

> This PR with the patch I suggested applied. It's just one data point of course, but it's encouraging at least. The values do seem to be monotonically increasing too, though I suspect if you zoomed in on a small enough interval that might change as you see more "noise". This is probably true of all the special functions though.  
  
I added some very basic testing to see if `ibeta` is monotonically increasing. It's more of a sanity check, but maybe that is the best that we can do? Note, the tests flat out fail for `floats` but that's probably acceptable.   
  
```C++  
// Bug testing for large a,b and x close to a / (a+b). See PR 1363.  
// The values for a,b are just too large for floats to handle.  
if (!std::is_same<T, float>::value)  
{  
   T a_values[2] = {10000000272564224, 3.1622776601699636e+16};  
   T b_values[2] = {9965820922822656, 3.130654883566682e+18};  
   T delta[8] = {0, 1e-15, 1e-14, 1e-13, 1e-12, 1e-11, 1e-10, 1e-9};  
   T a, b, x;  
   for (unsigned int i=0; i<2; i++){  
      a = a_values[i];  
      b = b_values[i];  
      x = a / (a+b); // roughly the median of ibeta  
  
        
      for (unsigned int j=0; j < 7; j++)  
      {  
         BOOST_CHECK(boost::math::ibeta(a, b, x + delta[j+1]) > boost::math::ibeta(a, b, x + delta[j]));  
         BOOST_CHECK(boost::math::ibeta(a, b, x - delta[j]) > boost::math::ibeta(a, b, x - delta[j+1]));    
      }  
   }  
}  
```

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2026-02-21 11:51:46 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3938664573  

```  
 x = a / (a+b); // roughly the median of ibeta  
```  
  
I don't think you're testing the saddle point here, shouldn't this be `(std::max)(a, b) / (a + b)` ?

---

## Comment 20

> Username: JacobHass8  
> Created_at: 2026-02-21 18:17:09 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3939197697  

> I don't think you're testing the saddle point here, shouldn't this be `(std::max)(a, b) / (a + b)` ?  
  
What do you mean by the saddle point? `a/(a+b)` is where `ibeta`~0.5 as per [wikipedia](https://en.wikipedia.org/wiki/Beta_distribution#Median).

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2026-02-21 18:42:15 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3939227510  

> What do you mean by the saddle point? a/(a+b) is where ibeta~0.5 as per [wikipedia](https://en.wikipedia.org/wiki/Beta_distribution#Median).  
  
Never mind, I may well have that wrong!

---

## Comment 22

> Username: JacobHass8  
> Created_at: 2026-02-21 18:57:35 UTC  
> Updated_at: 2026-02-22 22:22:02 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3939252351  

I'm not  sure why the macos tests are failing. Should I put in a check to see if the values are `nan`? Also, is there a better way to check if one value is greater than another using the boost test framework? Something like `BOOST_CHECK_LESS_THAN`. The current error output isn't very friendly.   
  
Disregard the above comment. I realized I could use `BOOST_CHECK_MESSAGE` to debug.

---

## Comment 23

> Username: JacobHass8  
> Created_at: 2026-02-23 04:38:15 UTC  
> Updated_at: 2026-02-23 04:40:51 UTC  
> Url: https://github.com/boostorg/math/pull/1363#issuecomment-3942558309  

@jzmaddock it looks like everything is squared away _except_ macos. I'm not really sure what to do about this because I'm running linux. The tests aren't even close to correct. The monotonicty test is failing with the error  
```bash  
Tests run with Clang version 17.0.0 (clang-1700.0.13.5), libc++ version 190102, Mac OS  
Testing spot values with type double  
./test_ibeta.hpp:508: error: in "test_main": ibeta not monotonically increasing above a/(a+b) for ibeta(10000000272564224, 9965820922822656, 0.50085594650495679) and delta=1e-10: [0.72326017914593033 >= 0.80645954095361849]  
./test_ibeta.hpp:508: error: in "test_main": ibeta not monotonically increasing above a/(a+b) for ibeta(31622776601699636, 3.1306548835666821e+18, 0.010000000000005001) and delta=9.9999999999999998e-13: [0.63702741474523428 >= 0.9052874276409012]  
./test_ibeta.hpp:510: error: in "test_main": ibeta not monotonically increasing below a/(a+b) for ibeta(31622776601699636, 3.1306548835666821e+18, 0.010000000000005001) and delta=9.9999999999999998e-13: [0.069925125891538167 >= 0.75500647219182093]  
```  
For reference, here are the tests  
<details><summary>Details</summary>  
<p>  
  
```C++  
T a_values[2] = {10000000272564224, 3.1622776601699636e+16};  
T b_values[2] = {9965820922822656, 3.130654883566682e+18};  
T delta[8] = {0, 1e-15, 1e-14, 1e-13, 1e-12, 1e-11, 1e-10, 1e-9};  
T a, b, x;  
for (unsigned int i=0; i<2; i++){  
  a = a_values[i];  
  b = b_values[i];  
  x = a / (a+b); // roughly the median of ibeta  
  
  for (unsigned int j=0; j < 7; j++)  
  {  
     BOOST_CHECK_MESSAGE(boost::math::ibeta(a, b, x + delta[j+1]) > boost::math::ibeta(a, b, x + delta[j]),   
        "ibeta not monotonically increasing above a/(a+b) for ibeta(" << a << ", " << b << ", " << x << ") and delta=" << delta[j] << ": [" << boost::math::ibeta(a, b, x + delta[j+1]) << " >= " << boost::math::ibeta(a, b, x + delta[j]) << "]");  
     BOOST_CHECK_MESSAGE(boost::math::ibeta(a, b, x - delta[j]) > boost::math::ibeta(a, b, x - delta[j+1]),   
        "ibeta not monotonically increasing below a/(a+b) for ibeta(" << a << ", " << b << ", " << x << ") and delta=" << delta[j] << ": [" << boost::math::ibeta(a, b, x - delta[j]) << " >= " << boost::math::ibeta(a, b, x - delta[j+1]) << "]");    
  }  
}  
```  
</p>  
</details>   
The only thing I can think of is printin more debug information in the log files and seeing what is going wrong. I just want to see if we're using the "erf" approximation or not. I don't want to clog up the github CI pipeline though. Do you have any thoughts?

---
