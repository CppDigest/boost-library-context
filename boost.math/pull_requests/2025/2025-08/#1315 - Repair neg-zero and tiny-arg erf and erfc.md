# #1315 Repair neg-zero and tiny-arg erf and erfc [Merged]

> Username: ckormanyos  
> Created at: 2025-08-29 07:52:12 UTC  
> Updated at: 2025-08-29 17:08:21 UTC  
> Merged at: 2025-08-29 12:05:57 UTC  
> Closed at: 2025-08-29 12:05:57 UTC  
> Url: https://github.com/boostorg/math/pull/1315  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2025-08-29 08:03:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1315#pullrequestreview-3167790410  

📁 include/boost/math/special_functions/erf.hpp

```diff
 161 |+       const T term2 { 2 * z / constants::root_pi<T>() };
 162 |+ 
 163 |+       return invert ? 1 - term2 : term2;
```

> Username: jzmaddock  
> Created_at: 2025-08-29 08:03:27 UTC  
> Url: https://github.com/boostorg/math/pull/1315#discussion_r2309478039  

Well done, I realized when I was lying in bed last night that `1` on it's own didn't quite cut it!

> Username: ckormanyos  
> Created_at: 2025-08-29 08:16:37 UTC  
> Url: https://github.com/boostorg/math/pull/1315#discussion_r2309506260  

It was a great review john. All you had to do was get me thinking. Thx.

> Username: ckormanyos  
> Created_at: 2025-08-29 08:19:45 UTC  
> Url: https://github.com/boostorg/math/pull/1315#discussion_r2309513065  

Actually John (@jzmaddock) in my new code, I think the final path for identically zero is not needed. That will be picked up by the first path, right?

> Username: ckormanyos  
> Created_at: 2025-08-29 08:22:56 UTC  
> Updated_at: 2025-08-29 08:23:06 UTC  
> Url: https://github.com/boostorg/math/pull/1315#discussion_r2309520165  

But then for the case of identically zoro, we would compute term, which is a waste of time.  
  
Is this a case for `(boost::math::tools::fpclassify)(z)` since you check for NaN also?

> Username: ckormanyos  
> Created_at: 2025-08-29 09:09:14 UTC  
> Url: https://github.com/boostorg/math/pull/1315#discussion_r2309625440  

I am moving toward this implementation locally. I will also throw in a few more edge-case Multiprecision tests locally and wrap this one up shortly.  
  
```  
template <class T, class Policy, class Tag>  
T erf_imp(T z, bool invert, const Policy& pol, const Tag& t)  
{  
   BOOST_MATH_STD_USING  
  
   BOOST_MATH_INSTRUMENT_CODE("Generic erf_imp called");  
  
   if ((boost::math::isnan)(z))  
      return policies::raise_domain_error("boost::math::erf<%1%>(%1%)", "Expected a finite argument but got %1%", z, pol);  
  
   const T abs_z { fabs(z) };  
  
   const bool signbit_result = ((boost::math::signbit)(z) != 0);  
  
   if (abs_z < tools::root_epsilon<T>())  
   {  
      if (abs_z < tools::epsilon<T>())  
      {  
         return invert ? T(1) : (!signbit_result) ? T(0) : -T(0);  
      }  
  
      // Series[Erf[x], {x, 0, 4}]  
      // Series[Erfc[x], {x, 0, 4}]  
  
      const T term2 { z * 2 / constants::root_pi<T>() };  
  
      return invert ? 1 - term2 : term2;  
   }  
  
   if (signbit_result)  
   {  
      if(!invert)  
         return -erf_imp(T(-z), invert, pol, t);  
      else  
         return 1 + erf_imp(T(-z), false, pol, t);  
   }  
  
   T result;  
  
   if(!invert && (z > detail::erf_asymptotic_limit<T, Policy>()))  
   {  
      detail::erf_asympt_series_t<T> s(z);  
      std::uintmax_t max_iter = policies::get_max_series_iterations<Policy>();  
      result = boost::math::tools::sum_series(s, policies::get_epsilon<T, Policy>(), max_iter, 1);  
      policies::check_series_iterations<T>("boost::math::erf<%1%>(%1%, %1%)", max_iter, pol);  
   }  
   else  
   {  
      T x = z * z;  
      if(z < 1.3f)  
      {  
         // Compute P:  
         // This is actually good for z p to 2 or so, but the cutoff given seems  
         // to be the best compromise.  Performance wise, this is way quicker than anything else...  
         result = erf_series_near_zero_sum(z, pol);  
      }  
      else if(x > 1 / tools::epsilon<T>())  
      {  
         // http://functions.wolfram.com/06.27.06.0006.02  
         invert = !invert;  
         result = exp(-x) / (constants::root_pi<T>() * z);  
      }  
      else  
      {  
         // Compute Q:  
         invert = !invert;  
         result = z * exp(-x);  
         result /= boost::math::constants::root_pi<T>();  
         result *= upper_gamma_fraction(T(0.5f), x, policies::get_epsilon<T, Policy>());  
      }  
   }  
  
   return ((!invert) ? result : 1 - result);  
}  
```

> Username: ckormanyos  
> Created_at: 2025-08-29 09:10:00 UTC  
> Url: https://github.com/boostorg/math/pull/1315#discussion_r2309627224  

This is the _generic_ implementation of `erf`/`erfc` in `erf_imp`. So it is used for MP types.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-08-29 09:03:53 UTC  
> Updated_at: 2025-08-29 17:08:21 UTC  
> Url: https://github.com/boostorg/math/pull/1315#issuecomment-3236289060  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1315?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.10%. Comparing base ([`4323902`](https://app.codecov.io/gh/boostorg/math/commit/43239024883d0f4986bdfce4cb471b3563faf161?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`c1d2bec`](https://app.codecov.io/gh/boostorg/math/commit/c1d2becb56cd40d9f1e3c1f6771ab93e7b1f2265?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1315/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1315?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1315   +/-   ##  
========================================  
  Coverage    95.10%   95.10%             
========================================  
  Files          797      797             
  Lines        67118    67126    +8       
========================================  
+ Hits         63833    63841    +8       
  Misses        3285     3285             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1315?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/erf.hpp](https://app.codecov.io/gh/boostorg/math/pull/1315?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Ferf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VyZi5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_erf.hpp](https://app.codecov.io/gh/boostorg/math/pull/1315?src=pr&el=tree&filepath=test%2Ftest_erf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VyZi5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1315?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1315?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4323902...c1d2bec](https://app.codecov.io/gh/boostorg/math/pull/1315?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
