# #562 `denorm_min` is not zero [Merged]

> Username: ryanelandt  
> Created at: 2023-08-18 15:11:47 UTC  
> Updated at: 2023-08-21 16:41:26 UTC  
> Merged at: 2023-08-21 16:39:19 UTC  
> Closed at: 2023-08-21 16:39:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562  

A type `T` is specialized if `std::numeric_limits<T>::is_specialized` is true. For specialized floating point types that support denorms, `denorm_min` should be less than `min`. For specialized floating point types that don't support denorms, `denorm_min` should be equal to `min`, e.g., see [this](https://en.cppreference.com/w/cpp/types/numeric_limits/denorm_min) or [this](https://github.com/boostorg/math/pull/1012#discussion_r1287102732).  
  
The types `cpp_bin_float` and `cpp_dec_float` are floating point types that do not support denorms. This PR changes the values of `denorm_min` for these types from `0` to `min`.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-08-19 09:19:07 UTC  
> Updated_at: 2023-08-19 09:19:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1684902129  

Great PR it is awesome to see the formal correctness of the types get _even_ closer to those of built-ins.  
  
Thanks Ryan (@ryanelandt)  
  
@jzmaddock I can add super-simple test case(s) for these if you think that's a good idea and handle this PR. Thoughts?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-08-19 11:20:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1684921818  

> @jzmaddock I can add super-simple test case(s) for these if you think that's a good idea and handle this PR. Thoughts?  
  
Yes please!  My apologies for being AWAL recently, hopefully I'll start catching up soon...  
  
A trivial change to test_numeric_limits.cpp would do it.... we also need to fix up at least gmp.hpp and mpfr.hpp which have the same mistakes.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2023-08-19 12:13:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1684930504  

> trivial change to test_numeric_limits.cpp would do it.... we also need to fix up at least gmp.hpp and mpfr.hpp which have the same mistakes  
  
Great. I can try this (if I may), ... get back into the game. Some of the recent stuff around here has been a bit too challenging for me. This one seems like a good one I could try.  
  
Cc: @jzmaddock and @ryanelandt

---

## Comment 4

> Username: ryanelandt  
> Created_at: 2023-08-19 14:10:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1684986095  

@ckormanyos feel free to make the necessary changes.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2023-08-20 05:38:50 UTC  
> Updated_at: 2023-08-20 05:40:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685183390  

Hi John (@jzmaddock) and Ryan (@ryanelandt) before I get started adding tests and the other two backends, a question, ...  
  
Does the presence of (now or soon-to-be) non-zero `denorm_min()` mean that `has_denorm` needs to be changed to something _other_ _than_ `std::denorm_absent`?

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2023-08-20 06:29:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685200094  

>  presence of (now or soon-to-be) non-zero `denorm_min()` mean that `has_denorm` needs to be changed  
  
Ahhh OK, it is clear from the original post. My bad, ... it is clear from the description use `has_denorm` as _absent_ and then `denorm_min()` is `min()`.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2023-08-20 09:30:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685236945  

Hi Ryan (@ryanelandt)  
- The changes also to MPFR and GMP are in and I also added a few basic limits tests.  
- CI is cycling.  
  
Cc: @jzmaddock

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2023-08-20 12:20:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685270600  

>  changes also to MPFR and GMP  
  
And `mpfi`. I had also forgotten a few needed `(min)()` values. CI is cycling again and looking better...

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2023-08-20 12:34:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685273744  

OK we have the change set, it's green on the new tests.  
  
Ryan (@ryanelandt) and John (@jzmaddock) if you see anything i missed or suggest more testing depth, I'd be happy to go another round. Otherwise, form my side, this one is ready to merge.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2023-08-20 12:56:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685278799  

Ryan (@ryanelandt) thanks again and super good catch!

---

## Comment 11

> Username: ryanelandt  
> Created_at: 2023-08-20 14:26:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685298816  

Thank you for completing this PR for me. I think it looks good. I have a few questions about the unit tests.  
  
1. Why use `BOOST_TEST(a == b)` instead of `BOOST_CHECK_EQUAL(a, b)`? Is this to avoid needing to have print support for the quantities being tested?  
  
2. Could for example:  
```  
BOOST_TEST(!(std::numeric_limits<Number>::denorm_min() > (std::numeric_limits<Number>::min)()));  
BOOST_TEST(!(std::numeric_limits<Number>::denorm_min() < (std::numeric_limits<Number>::min)()));  
```  
Be replaced with:  
```  
BOOST_TEST(std::numeric_limits<Number>::denorm_min() == (std::numeric_limits<Number>::min)());  
```  
The latter in shorter, it also gives the expected result in the cases that `(std::numeric_limits<Number>::min)()` is `NaN`.  
  
3. Is the check that:  
```  
BOOST_TEST(FP_ZERO != (boost::math::fpclassify)(std::numeric_limits<Number>::denorm_min()));  
```  
necessary? Because of the check 2 lines above it, I think it's effect is to check if `BOOST_TEST(FP_ZERO != FP_NORMAL)`.

---

## Comment 12

> Username: ryanelandt  
> Created_at: 2023-08-20 14:32:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685300169  

BTW, based on local tests, this PR breaks [this test](https://github.com/boostorg/math/blob/dc10fc04b29a356c3cf12c2052c45b0c3b3da5f2/test/test_autodiff_7.cpp#L60C5-L61C75) in Boost Math.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2023-08-20 14:39:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685301734  

> BTW, based on local tests, this PR breaks  
  
Then we can't merge it. I can look tomorrow. Maybe we need help from Matt (@mborland)?

---

## Comment 14

> Username: ryanelandt  
> Created_at: 2023-08-20 15:39:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685314706  

I made a [fix for the Boost Math side](https://github.com/boostorg/math/pull/1015).

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2023-08-20 16:32:05 UTC  
> Updated_at: 2023-08-20 16:32:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685325257  

> made a [fix](https://github.com/boostorg/math/pull/1015)  
  
Cool. I'd say handle the fix in Math, then we return here. Thanks

---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-21 08:44:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/562#pullrequestreview-1586512410  

📁 test/test_cpp_bin_float.cpp

```diff
  92 |-    BOOST_CHECK_EQUAL(max_val / min_val, inf_val);
  93 |-    BOOST_CHECK_EQUAL(max_val / -min_val, -inf_val);
  96 |+    BOOST_CHECK_EQUAL(max_val / denorm_min_val, inf_val);
```

> Username: jzmaddock  
> Created_at: 2023-08-21 08:44:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299808375  

I would have both the new *and* old tests here.  Note that the new tests would have passed before (divide by zero).

> Username: ckormanyos  
> Created_at: 2023-08-21 10:38:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299935457  

OK


---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-21 08:46:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/562#pullrequestreview-1586514561  

📁 test/test_cpp_bin_float.cpp

```diff
 112 |    BOOST_CHECK_LE((max_val / 2.0000001) * 1.9999999999999999, max_val);
 109 |-    BOOST_CHECK_EQUAL(min_val * 2 - min_val, min_val);
 113 |+    BOOST_CHECK_EQUAL(denorm_min_val * 2 - denorm_min_val, denorm_min_val);
```

> Username: jzmaddock  
> Created_at: 2023-08-21 08:46:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299809797  

From lines 99-109 again I would test both min and denorm_min?

> Username: ckormanyos  
> Created_at: 2023-08-21 10:38:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299935555  

OK


---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-21 08:46:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/562#pullrequestreview-1586515086  

📁 test/test_cpp_bin_float.cpp

```diff
 220 |+    BOOST_CHECK_EQUAL(denorm_min_val + max_val, max_val);
 221 |+    BOOST_CHECK_EQUAL(max_val - denorm_min_val, max_val);
 222 |+    BOOST_CHECK_EQUAL(denorm_min_val - max_val, -max_val);
```

> Username: jzmaddock  
> Created_at: 2023-08-21 08:46:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299810149  

Same again.

> Username: ckormanyos  
> Created_at: 2023-08-21 10:39:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299935691  

OK


---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-21 08:46:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/562#pullrequestreview-1586515439  

📁 test/test_cpp_bin_float.cpp

```diff
 234 |+    BOOST_CHECK(boost::math::signbit(-denorm_min_val / -2) == 0);
 235 |+    BOOST_CHECK(boost::math::signbit(-denorm_min_val / 2));
 236 |+    test_type neg_zero = denorm_min_val * -denorm_min_val;
```

> Username: jzmaddock  
> Created_at: 2023-08-21 08:46:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299810391  

And again... sorry!!

> Username: ckormanyos  
> Created_at: 2023-08-21 10:39:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299935805  

OK


---

## Review 20 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-21 08:47:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/562#pullrequestreview-1586517169  

📁 test/test_numeric_limits.cpp

```diff
 126 |+          BOOST_TEST(std::numeric_limits<Number>::denorm_min() > 0);
 127 |+          BOOST_TEST(!(std::numeric_limits<Number>::denorm_min() > (std::numeric_limits<Number>::min)()));
 128 |+          BOOST_TEST(!(std::numeric_limits<Number>::denorm_min() < (std::numeric_limits<Number>::min)()));
```

> Username: jzmaddock  
> Created_at: 2023-08-21 08:47:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299811597  

I would check for equality here?

> Username: ckormanyos  
> Created_at: 2023-08-21 10:40:20 UTC  
> Updated_at: 2023-08-21 10:40:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#discussion_r1299936957  

I would go for all three of not-`.LT.`, not-`.GT.` _and_ is-`.EQ.`.


---

## Comment 21

> Username: jzmaddock  
> Created_at: 2023-08-21 08:48:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685914357  

Thanks Chris and Ryan!!  
  
Other than a few minor comments above, this looks good to me.

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2023-08-21 08:52:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1685920388  

> Other than a few minor comments above, this looks good to me  
  
Thank you John.  
  
This is a silly question, one that I've had for a while. Does anyone know how to simply accept all the suggested comments as a batch change that leads to a single renewed CI run? Or do I/we have to manually change all the codes? Rookie question, I know.

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2023-08-21 10:38:36 UTC  
> Updated_at: 2023-08-21 10:40:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1686081060  

OK I can handle all these review suggestions. Please give me a day or so to do the editing and run CI.  
  
BTW, what is the reason that some of the sets of tests in `test_cpp_bin_float.cpp` seem to be disabled for ASAN and UBSAN?

---

## Comment 24

> Username: ryanelandt  
> Created_at: 2023-08-21 12:41:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1686255676  

@ckormanyos there is a lot of code duplication testing things for both `denorm_min` and `min_val`. You might consider putting these tests in a lambda function that looks something like:  
```c++  
const auto fn_small_val_tests = [&](const T x){  
  // Shared tests go here using x instead of `denorm_min` and `min_val`  
};  
  
fn_small_val_tests(denorm_min);  
fn_small_val_tests(min_val);  
```

---

## Comment 25

> Username: ckormanyos  
> Created_at: 2023-08-21 16:38:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1686663705  

> might consider putting these tests in a lambda function  
  
Good catch Ryan, surely would be better. But, ... I've got refactoring goals on basically all files in Math, Multiprecision and in all of Boost for that matter.   
  
So my basic philosophy in the test files, which are known by us to be very non-modernized, is, ... leave it until we have a chance to do a dedicated refactor in something like a GSoC.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2023-08-21 16:39:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1686664078  

The failure is on a clone attempt on drone. Merging.

---

## Comment 27

> Username: ryanelandt  
> Created_at: 2023-08-21 16:41:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/562#issuecomment-1686667070  

@ckormanyos Thanks for getting this PR in!

---
