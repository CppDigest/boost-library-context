# #1007 Fix inverse_discrete_quantile for large guess [Merged]

> Username: Yuhta  
> Created at: 2023-07-31 03:17:17 UTC  
> Updated at: 2024-04-29 16:47:35 UTC  
> Merged at: 2023-08-24 12:31:37 UTC  
> Closed at: 2023-08-24 12:31:37 UTC  
> Url: https://github.com/boostorg/math/pull/1007  

If `guess` passed to `inverse_discrete_quantile` cannot be represented as floating point number, it is possible that `guess + 1` or `guess - 1` does not change the value at all and we are stuck in infinite loop inside `round_to_floor` or `round_to_ceil`.  Fix this by increase/decrease more than 1 in these cases.  
  
Example code to reproduce this:  
```c++  
boost::math::binomial_distribution<> dist(9079765771874083840, 0.561815);  
boost::math::quantile(dist, 0.0365346);  
```

---

## Review 1 [Commented]

> Username: Yuhta  
> Created_at: 2023-07-31 03:25:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1007#pullrequestreview-1553900714  

📁 test/test_binomial.cpp

```diff
 728 |+      RealType expected = 5101148604445669693;
 729 |+ #endif
 730 |+      BOOST_CHECK_EQUAL(quantile(dist, 0.0365346), expected);
```

> Username: Yuhta  
> Created_at: 2023-07-31 03:25:16 UTC  
> Updated_at: 2023-07-31 03:29:22 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1278758588  

I can reproduce the issue with a standalone executable built with `g++ test.cc -I$PWD/include -std=c++17 -g -DBOOST_MATH_STANDALONE`.  However, when I try to put the test case here, the original version just passed without being stuck.  Is there any compiler flags in `b2` that make it behave differently?

> Username: Yuhta  
> Created_at: 2023-07-31 03:45:56 UTC  
> Updated_at: 2023-07-31 03:46:09 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1278765392  

I see it's because of `BOOST_MATH_DISCRETE_QUANTILE_POLICY`.  How can I test under the default value?

> Username: mborland  
> Created_at: 2023-07-31 14:53:46 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1279427847  

The default is `#define BOOST_MATH_DISCRETE_QUANTILE_POLICY integer_round_outwards`, but at the top of this file it is defined as `real`. You can pass a different policy to the distribution as a template parameter.


---

## Review 2 [Changes requested]

> Username: mborland  
> Created_at: 2023-08-01 13:19:33 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1007#pullrequestreview-1556978091  

Outside of the overflow this makes sense to me. We have been dealing with non-representable numbers in other places recently too: https://github.com/boostorg/math/pull/968

📁 test/test_binomial.cpp

```diff
 723 |+      // Accuracy is not too important here; the main purpose is to make
 724 |+      // sure it is not stuck.
 725 |+      BOOST_CHECK_CLOSE(quantile(dist, 0.0365346), 5101148604445670400, 1e12);
```

> Username: mborland  
> Created_at: 2023-08-01 13:15:18 UTC  
> Updated_at: 2023-08-01 13:19:33 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1280626828  

On MSVC platforms you are getting an overflow here: https://github.com/boostorg/math/actions/runs/5721216709/job/15503232562#step:11:2377. `boost::math::concepts::real_concept` is a long double wrapped in a class to check conceptual requirements for support of user defined types.

> Username: Yuhta  
> Created_at: 2023-08-05 03:23:12 UTC  
> Updated_at: 2023-08-05 13:38:40 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1284955660  

@mborland It's an unrelated bug in https://github.com/boostorg/math/blob/e682c1be3326a7b4ead43ff1c50149a4ce226e2e/include/boost/math/special_functions/beta.hpp#L489 where `a * log(x * c / a) + b * log(y * c / b))` is less accurate on MSVC because `long double` is just `double` there.  
  
Can I skip `real_concept` test on MSVC for this test case?

> Username: mborland  
> Created_at: 2023-08-07 13:39:13 UTC  
> Updated_at: 2023-08-07 13:39:23 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1285882299  

I ran your branch locally on an M1 Mac (double = long double) and that works fine. I should have time this week to investigate why MSVC wants to be different.

> Username: Yuhta  
> Created_at: 2023-08-07 15:42:58 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1286060455  

@mborland Note that in the newest version I skip the test for `real_concept` if `long double` is not precise enough.  I can reproduce the overflow on Linux if I force `real_concept_base_type` to be `double`.


---

## Comment 3

> Username: ethanyzhang  
> Created_at: 2023-08-17 21:12:50 UTC  
> Url: https://github.com/boostorg/math/pull/1007#issuecomment-1682988985  

@mborland Gentle ping :)

---

## Review 4 [Approved]

> Username: mborland  
> Created_at: 2023-08-18 12:06:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1007#pullrequestreview-1584448424  

Sorry about having this sit. Once the CI cycles I can merge it in. Beta is having other overflows documented here: https://github.com/boostorg/math/pull/1009. Like your test case it takes some extreme inputs to hit these so I am not overly concerned.

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-19 11:26:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1007#pullrequestreview-1585630257  

📁 test/test_binomial.cpp

```diff
 719 |+    // TODO: Generic ibeta_power_terms has accuracy issue when long
 720 |+    // double is not precise enough, causing overflow in this case.
 721 |+    if(!std::is_same<RealType, real_concept>::value ||
```

> Username: jzmaddock  
> Created_at: 2023-08-19 11:26:13 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1299174506  

What happens here when T is a double, does that still work?  Ideally we shouldn't be having spurious internal overflow as a) it breaks types with no infinity (this is potentially true for long double too) and b) it sets the hardware overflow flag which can trip up downstream consumers.

> Username: jzmaddock  
> Created_at: 2023-08-19 12:58:37 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1299183828  

The problem is in beta.hpp#478 onwards.  Don't see a trivial fix just yet :(  
  
inv_distrete_quantile.hpp#150 also needs adjusting to `else if((adder != 0) && (a + adder != a))` to speed up this new test case.

> Username: Yuhta  
> Created_at: 2023-08-19 23:30:24 UTC  
> Url: https://github.com/boostorg/math/pull/1007#discussion_r1299279037  

With built-in `double` and `long double` it works fine because Lanczos approximation is enabled.


---

## Comment 6

> Username: Yuhta  
> Created_at: 2023-08-19 23:33:01 UTC  
> Url: https://github.com/boostorg/math/pull/1007#issuecomment-1685131172  

@mborland It seems one CI job has environmental issue: https://drone.cpp.al/boostorg/math/1435/242/1  
Can you rerun it or I need to push again?

---

## Comment 7

> Username: mborland  
> Created_at: 2023-08-19 23:36:54 UTC  
> Url: https://github.com/boostorg/math/pull/1007#issuecomment-1685131578  

> @mborland It seems one CI job has environmental issue: https://drone.cpp.al/boostorg/math/1435/242/1  
>   
> Can you rerun it or I need to push again?  
  
I restarted it. The s390x runners can be finicky.

---

## Comment 8

> Username: Yuhta  
> Created_at: 2023-08-20 16:29:03 UTC  
> Url: https://github.com/boostorg/math/pull/1007#issuecomment-1685324677  

@mborland It fails with similar thing but on a different job: https://drone.cpp.al/boostorg/math/1450/66/1

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-04-28 14:50:16 UTC  
> Url: https://github.com/boostorg/math/pull/1007#issuecomment-2081510560  

@Yuhta : sorry to revisit this... but this, or at least this new test case is causing failures on MacOS/Clang, and after some investigation, it seems that Clang is not so much _wrong_ to fail, as other compilers are _lucky_ to pass.  
  
The issue is this: the binomial distribution for large parameters calls the incomplete Beta, and the incomplete Beta for parameters greater than about 1/epsilon essentially returns garbage.  There is no way around this that I can see - we go through all sorts sorts of contortions to try and avoid using logarithmic calculations and to preserve digits when we do - but in this case all the digits in the log calculation cancel out and we're left not even really knowing the order of magnitude of the result.  All the alternate methods of calculation I tried are essentially worse, with interval arithmetic yielding `[0, +INF]` as the bounds on the result.  Ouch.  
  
So... what was the use case here?  And what would happen if instead returning garbage we just gave up and threw an `evaluation_error` for large parameters?  I'm not saying that that is the correct way to go, just can't think of anything better right now...  
  
See also discussion inhttps://github.com/boostorg/math/pull/1125.  
  
CC @mborland

---

## Comment 10

> Username: Yuhta  
> Created_at: 2024-04-29 15:03:12 UTC  
> Url: https://github.com/boostorg/math/pull/1007#issuecomment-2082982858  

@jzmaddock We are ok with `evaluation_error`, before my fix it was stuck there forever, we just wanted to avoid that situation, the result in this case is not usable anyway.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2024-04-29 16:47:34 UTC  
> Url: https://github.com/boostorg/math/pull/1007#issuecomment-2083204516  

Thanks @Yuhta , I've decided to just disable the test for apple/clang for now - it does return, but returns garbage.

---
