# #1297 Add `logit`, `logistic_sigmoid`, and use in `logistic` dist to support promotion policies [Merged]

> Username: mborland  
> Created at: 2025-08-12 11:41:04 UTC  
> Updated at: 2025-08-14 17:17:21 UTC  
> Merged at: 2025-08-14 12:09:01 UTC  
> Closed at: 2025-08-14 12:09:01 UTC  
> Url: https://github.com/boostorg/math/pull/1297  

Closes: #1294   
Closes: #1296

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2025-08-12 12:15:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1297#pullrequestreview-3110318275  

📁 include/boost/math/special_functions/logistic_sigmoid.hpp

```diff
  22 |+     using promoted_real_type = typename policies::evaluation<RealType, Policy>::type;
  23 |+ 
  24 |+     std::fexcept_t flags;
```

> Username: jzmaddock  
> Created_at: 2025-08-12 12:15:47 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2269650242  

Rather than messing with the exception flags, I would tend to add two special cases:  
  
```  
if(-x >= tools::log_max_value<T>()) return 0;  
if(-x <= -tools::log_max_value<T>()) return 1;  
```  
  
Which has the advantage of working for types T with no infinity.


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2025-08-12 12:23:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1297#pullrequestreview-3110351749  

📁 include/boost/math/special_functions/logit.hpp

```diff
  24 |+     using promoted_real_type = typename policies::evaluation<RealType, Policy>::type;
  25 |+ 
  26 |+     std::fexcept_t flags;
```

> Username: jzmaddock  
> Created_at: 2025-08-12 12:23:32 UTC  
> Updated_at: 2025-08-12 12:23:33 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2269668062  

I *think* the exception checks are unnecessary here: things can only go bad if p is so small that `(1-p)/p` overflows which implies that p is denormal.  I'm not sure what the std says about `log(+INF)` but I suspect we should filter out that case anyway?  
  
So I guess I'm suggesting that we return the result of policies::raise_overflow_error when `p < tools::min_value<T>()`.  All other p values should return finite results.


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-08-12 12:23:55 UTC  
> Url: https://github.com/boostorg/math/pull/1297#issuecomment-3179103259  

Thanks for this Matt!

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-08-12 13:57:05 UTC  
> Updated_at: 2025-08-14 17:17:21 UTC  
> Url: https://github.com/boostorg/math/pull/1297#issuecomment-3179462149  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1297?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.07%. Comparing base ([`4d7d6e1`](https://app.codecov.io/gh/boostorg/math/commit/4d7d6e1982ec8c663c95853956789119ff9d230a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`f87d826`](https://app.codecov.io/gh/boostorg/math/commit/f87d826adf8c69bf010eced2de9456b9503ffffc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 28 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1297/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1297      +/-   ##  
===========================================  
+ Coverage    91.51%   95.07%   +3.55%       
===========================================  
  Files          558      796     +238       
  Lines        53881    67020   +13139       
===========================================  
+ Hits         49311    63717   +14406       
+ Misses        4570     3303    -1267       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1297?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/logistic.hpp](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Flogistic.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbG9naXN0aWMuaHBw) | `80.25% <100.00%> (-0.96%)` | :arrow_down: |  
| [include/boost/math/policies/policy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fpolicies%2Fpolicy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3BvbGljaWVzL3BvbGljeS5ocHA=) | `95.83% <ø> (-4.17%)` | :arrow_down: |  
| [.../boost/math/special\_functions/logistic\_sigmoid.hpp](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Flogistic_sigmoid.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2xvZ2lzdGljX3NpZ21vaWQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/logit.hpp](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Flogit.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2xvZ2l0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/math\_unit\_test.hpp](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&filepath=test%2Fmath_unit_test.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9tYXRoX3VuaXRfdGVzdC5ocHA=) | `22.95% <100.00%> (ø)` | |  
| [test/test\_logistic\_sigmoid.cpp](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&filepath=test%2Ftest_logistic_sigmoid.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2xvZ2lzdGljX3NpZ21vaWQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_logit.cpp](https://app.codecov.io/gh/boostorg/math/pull/1297?src=pr&el=tree&filepath=test%2Ftest_logit.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2xvZ2l0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [336 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1297/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1297?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1297?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4d7d6e1...f87d826](https://app.codecov.io/gh/boostorg/math/pull/1297?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 5 [Commented]

> Username: WarrenWeckesser  
> Created_at: 2025-08-12 16:59:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1297#pullrequestreview-3111840144  

That was fast!  
  
I have a question below about using the promoted type with the location/scale calculations that occur outside of the  calls of `logit` or `logistic_sigmoid`.  
  
Also a couple minor nitpicks involving minus signs.

📁 include/boost/math/distributions/logistic.hpp

```diff
 152 |-           return 1;
 153 |-        return 1 / (1 + exp(power)); 
 150 |+        RealType power = -(location - x) / scale;
```

> Username: WarrenWeckesser  
> Created_at: 2025-08-12 16:40:23 UTC  
> Updated_at: 2025-08-12 16:59:36 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2270529240  

Here and below: shouldn't this calculation also use the promoted type?  E.g. suppose `location` is 0, `scale` is 3, and `x` is 1.  If `RealType` is double precision, then `power` is `0.3333333333333333`, which becomes `0.33333333333333331483` when promoted to extended precision.  If the translation and scaling uses the promoted type, then `power` should be `0.33333333333333333334`, and that is the value that I would expect to be passed to `logistic_sigmoid`.

> Username: mborland  
> Created_at: 2025-08-13 09:22:40 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2272672805  

Having `power` as a promoted type makes sense. I then added the ability to disable further promotion to `logistic_sigmoid`. I was thinking about this case with the policy from your original issue[1]: `RealType` is `float` which means that `power` would be promoted to `double`. Without further disabling promotion `logistic_sigmoid` would compute internally using `long double` rather than staying at the already promoted type of `double`.   
  
[1]  
```  
typedef policy<  
    promote_float<true>,  
    promote_double<true>  
> with_promotion;  
```

> Username: WarrenWeckesser  
> Created_at: 2025-08-13 23:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2274841238  

Yeah, I was wondering how disabling a second promotion would work out.  
  
Adding that boolean template parameter to the function seems a bit awkward.  Now `logistic_sigmoid` has a regular Policy parameter, *and* a parameter to disable promotion in that Policy.  Most users of `logistic_sigmoid` would never need it--they could just use an appropriately defined Policy.  Also, I think this boolean parameter would have to be added to any other special functions that get called from within distribution functions (assuming an audit of the other distributions finds more cases where promotion isn't fully implemented).  
  
Instead, in the `cdf` function, could `policies::normalise` be used to define a new policy from the existing one, with promotion disabled? This appears to be the suggestion given at the bottom of https://www.boost.org/doc/libs/1_87_0/libs/math/doc/html/math_toolkit/pol_tutorial/policy_tut_defaults.html.  
  
So something like  
  
     normalise<Policy, promote_float<false>, promote_double<false>>::type  
  
would be the policy used when, say, `cdf` calls `logistic_sigmoid`.  
  
This worked for a trivial example that I created with `lambert_w0` (just to test the idea), but I haven't tried updating this PR with such a change, so I don't know if there are obstacles to making it work.

> Username: jzmaddock  
> Created_at: 2025-08-14 08:29:26 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2275893632  

This is how all the special functions avoid double-promotion when calling each other, there's a bit of boilerplate in the function prologue that looks like:  
  
```  
   typedef typename policies::normalise<  
      Policy,  
      policies::promote_float<false>,  
      policies::promote_double<false>,  
      policies::discrete_quantile<>,  
      policies::assert_undefined<> >::type forwarding_policy;  
```  
  
And then `forwarding_policy` is passed down to any called functions.  If I'd had a bit more foresight, I might have added that as a member typedef of class `policy`.

> Username: mborland  
> Created_at: 2025-08-14 08:44:06 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2275928566  

Thanks John. I've added a `struct make_forwarding_policy` and `using make_forwarding_policy_t` so that we don't have to keep creating the policy. The call then looks like:  
  
```  
return logistic_sigmoid(power, policies::make_forwarding_policy_t<Policy>());  
```

---

📁 include/boost/math/distributions/logistic.hpp

```diff
 152 |-           return 1;
 153 |-        return 1 / (1 + exp(power)); 
 150 |+        RealType power = -(location - x) / scale;
```

> Username: WarrenWeckesser  
> Created_at: 2025-08-12 16:43:41 UTC  
> Updated_at: 2025-08-12 18:10:23 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2270536573  

Very minor nitpick: The extra `-` seems unnecessary. `(x - location)/scale` would be the usual translation from the `(location, scale)` family to the standard value.

---

📁 include/boost/math/distributions/logistic.hpp

```diff
 231 |-        //return -scale*log(1/p-1) + location;
 232 |-        return location - scale * log((1 - p) / p);
 223 |+        return location - scale * -logit(p, Policy());
```

> Username: WarrenWeckesser  
> Created_at: 2025-08-12 16:48:51 UTC  
> Updated_at: 2025-08-12 16:59:36 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2270547763  

Another very minor nitpick: remove the double negative, so the expression is `location + scale * logit(p, Policy())`.  That's the usual expression for going from a 'standard' value to a value in the `(location, scale)` family.  I assume the original `-` in front of `scale` was there because `log((1 - p)/p)` was used instead of `log(p/(1 - p))`.


---

## Review 6 [Commented]

> Username: WarrenWeckesser  
> Created_at: 2025-08-12 17:14:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1297#pullrequestreview-3111936519  

📁 include/boost/math/distributions/logistic.hpp

```diff
 147 |           return result;
 148 |        }
 149 |        BOOST_MATH_STD_USING
```

> Username: WarrenWeckesser  
> Created_at: 2025-08-12 17:14:57 UTC  
> Url: https://github.com/boostorg/math/pull/1297#discussion_r2270601785  

It looks like this is no longer necessary (likewise in `quantile`, and in the complemented versions).


---

## Comment 7

> Username: WarrenWeckesser  
> Created_at: 2025-08-14 15:21:38 UTC  
> Url: https://github.com/boostorg/math/pull/1297#issuecomment-3188852112  

Thanks @mborland!

---

## Comment 8

> Username: mborland  
> Created_at: 2025-08-14 15:26:11 UTC  
> Url: https://github.com/boostorg/math/pull/1297#issuecomment-3188867168  

> Thanks @mborland!  
  
You're welcome! Since I have some time I'm looking through the other distributions to see where else we need to apply manual promotion.

---
