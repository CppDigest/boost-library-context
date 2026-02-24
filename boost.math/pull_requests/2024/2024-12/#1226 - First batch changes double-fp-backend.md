# #1226 First batch changes double-fp-backend [Merged]

> Username: ckormanyos  
> Created at: 2024-12-19 08:33:20 UTC  
> Updated at: 2024-12-19 20:46:07 UTC  
> Merged at: 2024-12-19 20:45:59 UTC  
> Closed at: 2024-12-19 20:45:59 UTC  
> Url: https://github.com/boostorg/math/pull/1226  

I need to make some tiny changes in the Math tests when testing the candidate `double_fp_backend<double>` as a multiprecision backend.  
  
The min./max. exponents are really small (comparatively) for a multiple-precision type. I'm running the tests for _double-double_, which has `min_exponent10` of $-291$ and `max_exponent10` of $308$. So this causes a few over/underflows unexpectedly for a multiprecision type under test.  
  
A second batch of similarly small, but more difficult-to-make changes will be needed. But I will do these in a second step, pending approval of the first, simpler batch.  
  
Cc: @jzmaddock and @mborland

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-12-19 09:40:57 UTC  
> Updated_at: 2024-12-19 09:51:41 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2553209008  

See also [PR154 in GSoC repo](https://github.com/BoostGSoC21/multiprecision/pull/154)  
  
Cc: @sinandredemption and @cosurgi

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-12-19 09:55:08 UTC  
> Updated_at: 2024-12-19 13:14:19 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2553282610  

One example of a change needed comes from the test point of the irregular Bessel function $K_{\nu}\left(x\right)$, for $x{\in}\mathbb{R}$.  
  
We need to compute  
  
$$  
K_{-1000}\left(700\right)\text{,}  
$$  
  
which internally needs to compute the value of  
  
$$  
e^{-700}{\approx}10^{-305}\text{.}  
$$  
  
This number, however, underflows `cpp_double_fp_backend<double>` for 64-bit double, having `min_exponent10` of $-291$.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-12-19 10:00:56 UTC  
> Updated_at: 2024-12-19 18:35:00 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2553294929  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1226?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.83%. Comparing base [(`699d79b`)](https://app.codecov.io/gh/boostorg/math/commit/699d79b54f5ab99b8f64eddd98f4fb0ff3d335f1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`214e19c`)](https://app.codecov.io/gh/boostorg/math/commit/214e19cc5183992f899defe028978eeef95dd29c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1226/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1226?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1226      +/-   ##  
===========================================  
- Coverage    93.83%   93.83%   -0.01%       
===========================================  
  Files          657      657                
  Lines        55241    55241                
===========================================  
- Hits         51836    51833       -3       
- Misses        3405     3408       +3       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1226?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/math/special\_functions/detail/bessel\_k0.hpp](https://app.codecov.io/gh/boostorg/math/pull/1226?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_k0.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfazAuaHBw) | `100.00% <ø> (ø)` | |  
| [test/cubic\_roots\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1226?src=pr&el=tree&filepath=test%2Fcubic_roots_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jdWJpY19yb290c190ZXN0LmNwcA==) | `92.47% <100.00%> (ø)` | |  
| [test/linear\_regression\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1226?src=pr&el=tree&filepath=test%2Flinear_regression_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9saW5lYXJfcmVncmVzc2lvbl90ZXN0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/quartic\_roots\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1226?src=pr&el=tree&filepath=test%2Fquartic_roots_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9xdWFydGljX3Jvb3RzX3Rlc3QuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_bessel\_i.hpp](https://app.codecov.io/gh/boostorg/math/pull/1226?src=pr&el=tree&filepath=test%2Ftest_bessel_i.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9pLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_bessel\_j.hpp](https://app.codecov.io/gh/boostorg/math/pull/1226?src=pr&el=tree&filepath=test%2Ftest_bessel_j.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9qLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1226/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1226?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1226?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [699d79b...214e19c](https://app.codecov.io/gh/boostorg/math/pull/1226?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-12-19 13:59:30 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554146648  

Hi John (@jzmaddock) and Matt (@mborland) there seem to be some tolerance issues in files like `linear_regression_test.cpp` on the clang-port to MSVC 2022.  
  
I'm not qualified to "up" the relevant tolerances. Does anyone know if these failures are relevant? Is it just a tolerance issue? Or does something actually need to be done?  
  
I'm not entirely sure, but I do not think my changes in areas like Bessel functions are related. But you never know in Math. Anyway, could you guys take a look? Thanks.

---

## Comment 5

> Username: mborland  
> Created_at: 2024-12-19 14:23:00 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554272805  

Github updated the version of MSVC2022 and it caused three tests to fail consistently. I can nudge up the tolerance a bit because it's been consistent across runs instead of sporadic like normal tolerance problems.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-12-19 14:25:22 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554285281  

> I can nudge up the tolerance a bit  
  
Should I just do that here while I'm on such a roll? If that's what's to be done, I can handle it. Thx Matt (@mborland)

---

## Comment 7

> Username: mborland  
> Created_at: 2024-12-19 14:27:41 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554298338  

> > I can nudge up the tolerance a bit  
>   
> Should I just do that here while I'm on such a roll? If that's what's to be done, I can handle it. Thx Matt (@mborland)  
  
Please do

---

## Comment 8

> Username: cosurgi  
> Created_at: 2024-12-19 14:34:53 UTC  
> Updated_at: 2024-12-19 14:36:15 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554334677  

Wow, @ckormanyos thank you very much for working on this. I am quite busy at the moment, so I only had a brief look, but please keep working. I don't remember much, but if it will be needed I will try to recall what I wrote in some of my code, when you will need it.

---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2024-12-19 14:36:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1226#pullrequestreview-2514998048  

📁 reporting/performance/test_kn.cpp

```diff
  49 |    { { SC_(100.0), SC_(5.0), SC_(7.03986019306167654653386616796116726248616158936088056952477e115) } },
  50 |    { { SC_(100.0), SC_(80.0), SC_(8.39287107246490782848985384895907681748152272748337807033319e-12) } },
  51 |-    { { SC_(-1000.0), SC_(700.0), SC_(6.51561979144735818903553852606383312984409361984128221539405e-31) } },
```

> Username: jzmaddock  
> Created_at: 2024-12-19 14:36:59 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892327278  

Given that the answer is within a sensible range this should work and give the correct answer for all T.  Do you know where in the code this is failing?

> Username: ckormanyos  
> Created_at: 2024-12-19 14:39:44 UTC  
> Updated_at: 2024-12-19 14:39:45 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892337404  

> where in the code this is failing?  
  
Yes John (@jzmaddock) that was a tough one. Internally the calculation must compute $e^{-700}$ which underflows the type.

> Username: jzmaddock  
> Created_at: 2024-12-19 14:50:45 UTC  
> Updated_at: 2024-12-19 14:50:46 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892374344  

Sure, but it works for type double which also can't compute e^-700.  Ah wait, are these calling the 113-bit precision rational approximations?

> Username: ckormanyos  
> Created_at: 2024-12-19 16:23:37 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892653241  

> Ah wait, are these calling the 113-bit precision rational approximations  
  
Yes John. This is a conversation I was kind of waiting to have. My exponent range is EVEN less than that of `double`. But I end up in the multiprecision section of the special functions.  
  
This worked surprisingly well and there seem to be very few errors. There are, however, a few we will need to hash out.

> Username: ckormanyos  
> Created_at: 2024-12-19 16:27:08 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892662001  

This somewhat novel type has $106$ digits, yet is limited to `min_exponent10` of $-291$ and `max_exponent10` of $+308$


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2024-12-19 14:38:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1226#pullrequestreview-2515003257  

📁 test/test_bessel_j.hpp

```diff
 283 |     #ifndef SYCL_LANGUAGE_VERSION
 284 |-     BOOST_CHECK_CLOSE_FRACTION(boost::math::cyl_bessel_j(364, T(38.5)), SC_(1.793940496519190500748409872348034004417458734118663909894e-309), tolerance);
 284 |+     BOOST_IF_CONSTEXPR (std::numeric_limits<T>::min_exponent10 <= -307)
```

> Username: jzmaddock  
> Created_at: 2024-12-19 14:38:05 UTC  
> Updated_at: 2024-12-19 14:38:06 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892331180  

Denormals not withstanding, this should work for all T as well - it should underflow to zero when the exponent does not have enough range?

> Username: ckormanyos  
> Created_at: 2024-12-19 14:41:33 UTC  
> Updated_at: 2024-12-19 16:27:19 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892343822  

> Denormals not withstanding, this should work for all T as well - it should underflow to zero when the exponent does not have enough range?  
  
Maybe my handling of zeros needs to be checked. I will recheck what's up in the double-fp backend.


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2024-12-19 14:39:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1226#pullrequestreview-2515007630  

📁 test/test_bessel_k.hpp

```diff
 135 |         {{ SC_(100.0), SC_(80.0), SC_(8.39287107246490782848985384895907681748152272748337807033319e-12) }},
 136 |-         {{ SC_(-1000.0), SC_(700.0), SC_(6.51561979144735818903553852606383312984409361984128221539405e-31) }},
 136 |+         (std::numeric_limits<T>::is_specialized && (std::numeric_limits<T>::min_exponent10 > -300))
```

> Username: jzmaddock  
> Created_at: 2024-12-19 14:39:00 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892334713  

As with the first comment, given that the result is in range, this should work for all T, do you know where things go wrong?

> Username: ckormanyos  
> Created_at: 2024-12-19 14:40:53 UTC  
> Url: https://github.com/boostorg/math/pull/1226#discussion_r1892341526  

Same as above.


---

## Comment 12

> Username: ckormanyos  
> Created_at: 2024-12-19 14:43:26 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554381009  

> thank you very much for working on this  
  
Thanks @cosurgi we are actually getting close. Conservatively, I think we can get the double-fp backend into summer 1.89. But I might even hope for April's 1.88. Let's see if we can get all the edge cases to _behave_. It's not that far off the mark now. I'd like to first get double-double stable and _in_, then in a second push charge on for quad.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2024-12-19 14:44:17 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554385531  

>>> I can nudge up the tolerance a bit  
  
>> Should I just do that here while I'm on such a roll? If that's what's to be done, I can handle it. Thx Matt (@mborland)  
  
> Please do  
  
Cycling now

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2024-12-19 16:36:59 UTC  
> Updated_at: 2024-12-19 16:37:46 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2554985853  

OK guys, CI is green. Yet the first batch of changes is not quite ready.  
  
For the type `cpp_double_double`, we still have to negotiate on:  
  
- the test of `cyl_bessel_j(364, 35.5)`  
- and the test of `cyl_bessel_k(-1000, 700)`  
  
 I consider the second point to be done. The sub-normal case is a bit more tricky and I still need to check if my zero-handling is correct in the backend. Then we'll see what's up on that test case.  
  
Cc: @jzmaddock   
  
Also @mborland the adjusted tolerances on the cases we mentioned are (perhaps liberal) but green.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2024-12-19 16:48:01 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555044179  

OK, I think I see the issue, min and max values are asymetric, so in bessel_k0.hpp:473 the line:  
  
```  
if(x < tools::log_max_value<T>())  
```  
  
Should actually be:  
  
```  
if(-x > tools::log_min_value<T>())  
```  
  
There will be other places as well... you caught me be lazy!!  But that (along with any other similar mistakes) should fix things.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2024-12-19 17:06:21 UTC  
> Updated_at: 2024-12-19 17:06:56 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555151024  

> OK, I think I see the issue, min and max values are asymetric,  
  
OK amazing catch John, let me run that thing through CI and see how it shakes out.  
  
> There will be other places as well...   
  
It's really rather easy-going. I have a really short remaining-issue-list with one single point of `ellint_3`, a whole bunch of `inv_gamma()` with GIANT exponents (I can deal with these), a few things more on $K_{\nu}$. But that's pretty much it. So all in all, the generic-ness of Math/Multiprecision is simply awesome.  
  
Let me see if we can dial in this double-trouble type ASAP.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2024-12-19 17:25:08 UTC  
> Updated_at: 2024-12-19 17:27:34 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555258109  

Ah OK. John with the change in `bessel_k0.hpp` it is very close. I do, however, still need to  
  
```  
#define BOOST_NO_LIMITS_COMPILE_TIME_CONSTANTS  
```  
  
Otherwise I get the vanilla hard-coded values for logarithms of max/min values. And I'm fine, perfectly fine defining this (at least preliminarily) in the `cpp_double_fp_backend` main header file until we hash out the rest of the wrinkles.  
  
Let me back out the changes in $K_{\nu}$ tests.  
  
Then the only open point is:  
  
$$  
J_{364}\left(35{\frac{1}{2}}\right)  
$$

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2024-12-19 17:28:35 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555275614  

> the only open point is  
  
I mean in this PR. There will be batch2 of specfun changes needed to negotiate coming when we finish batch1.

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2024-12-19 18:44:57 UTC  
> Updated_at: 2024-12-19 19:06:51 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555541002  

OK I needed to correct two wrongly-placed `constexpr` attributes.  
  
I need to move forward with this thing. So I just reverted the test-case-change on `cyl_bessel_j`. So the negotiation is in my view closed. If/when this thing cycles, I'd like to consider to merge it. Then I can charge ahead on with `cpp_double_fp_backend`...

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2024-12-19 19:07:48 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555581084  

> OK amazing catch John, let me run that thing through CI and see how it shakes out.  
  
What I meant to say was - "I tend to make the same mistake over and over" so since all the Bessel rational approximations are pretty similar, there are probably other places where we have this bug whether or not we happen to have (un)fortunate test values in the (un)sweet spot that triggers the issue.  
  
> Ah OK. John with the change in bessel_k0.hpp it is very close. I do, however, still need to  
>#define BOOST_NO_LIMITS_COMPILE_TIME_CONSTANTS  
  
That should definitely NOT be needed unless there's something wrong with your numeric_limits specialization?  
  
>Then the only open point is:  
> J 364 ( 35 1 2 )   
  
The result in that case is a denorm at double precision, is that the issue?

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2024-12-19 20:25:42 UTC  
> Updated_at: 2024-12-19 20:26:32 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555711814  

> What I meant to say was - "I tend to make the same mistake over and over"  
  
Me too.  
  
>> Ah OK. John with the change in bessel_k0.hpp it is very close. I do, however, still need to  
>> '#define BOOST_NO_LIMITS_COMPILE_TIME_CONSTANTS'  
  
> That should definitely NOT be needed unless there's something wrong with your numeric_limits specialization?  
  
Maybe. Or perhaps I don't yet have proper handling of underflow(s) and/or zeros. I'll take a bit more time to figure out what I really need to do. But that is my assignment.  
  
In this PR, there is basically now just one change of an exponent-check constant, two removals of the word `constexpr`, and the tolerance changes on the new runners. So I'm likely to push it, since there are no controversial changes there. But I believe the backend I'm testing might not quite be right (yet) in some of these edge cases. Let me figure out how to move forward. Then when I have some more well-formulated info, I'll pick up some of the harder points in a newer PR...

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2024-12-19 20:45:50 UTC  
> Url: https://github.com/boostorg/math/pull/1226#issuecomment-2555741891  

OK, so s390 is hanging on drone. Everything else is green. This thing will fix one single failure in the onslaught of `cpp_double_fp_backend` tests. There will be more work needed.  
  
There is nothing more controversial in this PR. I can play around with it since the erroneous `constexpr` attributes are corrected.  
  
Also the tolerances are corrected on some of those distributions mentioned above.  
  
So I'll push this thing.  
  
Then I need to study up on the backend details and find out exactly what is causing some of the final edge case failures. So this will be ocntinued in future PRs.  
  
Cc: @jzmaddock and @mborland

---
