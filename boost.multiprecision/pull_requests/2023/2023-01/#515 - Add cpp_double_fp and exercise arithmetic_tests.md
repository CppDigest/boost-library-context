# #515 Add cpp_double_fp and exercise arithmetic_tests [Closed]

> Username: ckormanyos  
> Created at: 2023-01-06 08:01:43 UTC  
> Updated at: 2025-06-27 21:11:02 UTC  
> Closed at: 2025-06-26 05:17:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515  

The purpose of this pull request is to add the `cpp_double_fp_backend<>` template class.  
  
In the initial commit, we:  
  
- Run the arithmetic tests.  
- Modify elementary function tests to prepare for `TEST_CPP_DOUBLE_FLOAT`, but do not run them yet.  
- Try some further small adaptions to `arithmetic_tests.hpp` that improve the testing logic for mixed-int128/float128 interaction.  
  
If this runs through, I can add the function tests (stuff like `sin()`, `exp()`, etc.) as a next step. These run well and properly in the GSoC fork, but are not yet exercised in this initial commit.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-01-06 09:07:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1373373143  

The first CI run (even with only the tiny step of arithmetic tests and modified but not running function tests) has failed due to a rookie typo involving a missing semicolon.  
  
Running again now...

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2023-01-06 11:28:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1373496430  

OK the last CI run passed on GHA but failed a silly typo on performance_test on Circle CI.  
  
The latest push goes a lot farther with an attempt for:  
- correcting the above and also  
- adding most of functions-and-limits.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2023-01-06 13:57:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1373676428  

OK.  
- GHA is running green with arithmetic tests and most of functions and limits.  
- In drone I think I forgot some needed quadmath inclusions in the JAMfile.  
- Circle CI failed on specfun_cpp_dec_float, which might be unrelated to the double-double work.  
  
I will press on trying to correct the Jamfile for the drone runs.  
  
After the next cycle, I can collect the know weaknesses - and there are a few, especially with 10-byte `long` `double` mysteries.  
  
And although this is going well, I'd like to resolve any known mysteries (maybe review needed) before pressing on to specfun and maybe more deep conversoin tests.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2023-01-06 16:53:56 UTC  
> Updated_at: 2023-01-06 16:55:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1373889736  

OK. I'll jump the CI a bit and think this latest commit might bring this work to a small juncture.  
  
At this juncture, coull you John (@jzmaddock) and Matt (@mborland) please take a cursory glance at the header/tests and give me a sanity check? If you see something waaayyy wrong or out of line, please give me a heads up?  
  
Most of the tests and things are handled by `cpp_double_fp_backend<>` now at the moment.  
  
Known problems include:  
1. Round-tripping and banker's rounding (which may be related) and seem to involve the junction between the low/high parts of the _double_-_fp_.  
2. Problems with edge-cases of 10-byte double-long-double on platforms having this 10-byte `long` `double` type. For instance, I get non-finite `log()` of double-long-double's `(max)()`, but only for 10-byte long double. I suppose there might be a classic, Chris, dont't you remember, ... something, or a few `L` suffixes, or similar.  
  
In light of this, I'll retreat to the fork a bit and try to look into at least the 10-byte `long` `double` mysteries prior to going in full for spce_fun.  
  
After solving the mysteries, going for spec_fun and setting up some rudimentary docs, this thing will actually be close to ready.  
  
Cc: @sinandredemption and @cosurgi

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2023-01-06 18:28:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/515#pullrequestreview-1239220465  

📁 test/test_pow_df.cpp

```diff
   1 |+ ///////////////////////////////////////////////////////////////
   2 |+ //  Copyright 2011 John Maddock.
```

> Username: jzmaddock  
> Created_at: 2023-01-06 18:28:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r1063678944  

Sorry for not seeing it, but how does this test differ from test_pow.cpp?

> Username: ckormanyos  
> Created_at: 2023-01-06 18:30:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r1063680503  

Hi John thanks for looking. The (new) test known as `test_pow_df.cpp` has parsed the original test and thrown away numbers that are so large that the arguments will definitely overflow `cpp_double_fp_backend<double>`.  
  
So instead of having about 650 listed cases and controls, there remain about 650.


---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-01-06 18:30:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1373988377  

@ckormanyos my apologies for not being able to help out much right now: but a quick scan over the sources shows nothing obviously wrong.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2023-01-06 18:32:53 UTC  
> Updated_at: 2023-01-06 18:33:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1373990034  

> a quick scan over the sources shows nothing obviously wrong  
  
Thanks John (@jzmaddock) I'm actually doing alright. I am always amazed how powerful and deep the Multiprecision test suite(s) are. These are wonderful tests.  
  
I've found a few dependencies on `libquadmath.a` in the final drone CI run that I will eliminate in the limits. Other than that (and the aforementioned problems with rounding and 10.byte long double) , I'm comfortable to simply keep going.

---

## Review 8 [Commented]

> Username: mborland  
> Created_at: 2023-01-07 15:04:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/515#pullrequestreview-1239676932  

By and large this looks good. I added some minor comments that are applicable in a number of places.

📁 include/boost/multiprecision/cpp_double_fp.hpp

```diff
  91 |+ #else
  92 |+ constexpr
  93 |+ #endif
```

> Username: mborland  
> Created_at: 2023-01-07 14:44:29 UTC  
> Updated_at: 2023-01-07 15:04:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r1064011704  

```diff  
-#endif  
+constexpr  
```  
  
Since we are moving to C++14 in the next release you should be able to replace all of these blocks with just `constexpr`.

> Username: ckormanyos  
> Created_at: 2023-01-07 20:59:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r1064046523  

Thanks Matt (@mborland) I'm doing a rather large rework of the numeric_limits for this work and I'll be doing a better job determining what is and is not really `constexpr` moving forward.

> Username: ckormanyos  
> Created_at: 2023-01-08 12:31:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r1064135060  

There's one more detail we'll need to handle. MSVC 2015 (also known as VC 14.0) might have difficulties handling C++14: Relaxed constexpr restrictions. Subjectively, I find this to be the case when working with this compiler locally and in CI.  
  
The _only_ compiler I'm using `BOOST_MP_CXX14_CONSTEXPR` for in my current PR drive is, in fact, VC 14.0. So if we drop that compiler, It'll be OK.  
  
It's problem seems to be multiple return statements in a `constexpr` function. But if you really think of it, I'm not really super-100% sure if multiple return statements are allowed in a C++14 `constexpr` function?  
  
So this might prove to be problematic when going C++14 as we move forward.

---

📁 include/boost/multiprecision/cpp_double_fp.hpp

```diff
 761 |+          for (;;)
 762 |+          {
 763 |+             if (static_cast<std::uint_fast8_t>(p_local & static_cast<std::uint32_t>(UINT8_C(1))) != static_cast<std::uint_fast8_t>(UINT8_C(0)))
```

> Username: mborland  
> Created_at: 2023-01-07 14:47:10 UTC  
> Updated_at: 2023-01-07 15:04:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r1064011934  

```diff  
-            if (static_cast<std::uint_fast8_t>(p_local & static_cast<std::uint32_t>(UINT8_C(1))) != static_cast<std::uint_fast8_t>(UINT8_C(0)))  
+            if (static_cast<std::uint_fast8_t>(p_local & static_cast<std::uint32_t>(1)) != static_cast<std::uint_fast8_t>(0))  
```  
  
Isn't it redundant to cast to `UINT8_C` and then to a different fixed width type?

> Username: ckormanyos  
> Created_at: 2023-01-07 21:01:24 UTC  
> Updated_at: 2023-01-07 21:01:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r1064046680  

Oh geez, I've gotten in the habit of always deocrating constants with one of the `UINTn_C`-styls macros. When doing this, I cast to the type and then use the macro needed for the width of the constant. I don't know if this makes sense to anyone else, but I use this kind of rule.


---

## Comment 9

> Username: ckormanyos  
> Created_at: 2023-01-08 13:37:56 UTC  
> Updated_at: 2023-01-08 13:38:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1374838269  

OK. This seems like it's about to go green, which means that the formal correctness of `cpp_double_fp_backend` is nearing completion.  
  
There are, nonetheless, a whole bunch of steps to do.  
  
- We are actually now farther ahead in the Boost.Multiprecision PR than on our Fork. So I will need to merge from Boost's branch "back to" our GSoC fork. I anticipate doing this today or tomorrow.  
- There are a bunch of open items needing attention.  
  
These include:  
- Banker's rounding is not fully working. Why? Should it work? Why or Why not?  
- The selection of `cpp_double_fp_backend::my_value_max()` is unclear. Can we justify this value?  
- `eval_exp()` and `eval_log()` need further optimization.  
- Optimize (or prove that is already optimized) the routine for division.  
- Optimize (or prove that is already optimized) the routine for `eval_sqrt()`.  
  
The only known _problem_ is with banker's rounding. Otherwise, it's just optimize, tests and docs.  
  
The `constexpr`-ification is moving along very well. In fact, large parts of the double-double backend are already now `constexpr`. For this, I have written quite a bit of `<cmath>` functionality in a true `constexpr`-fashion. Here I borrowed inspiration from Matt (@mborland) in `<ccmath.hpp>`. But there is more work to go. We need to constexpr-ify `::floor()` and maybe even `::log()` and/or `::exp()` for `float`, `double`, `long` `double` and `__float128`. This is a super-cool area to march forward in.  
  
The next step for Boost testing will be to run the `cpp_double_fp_backend` through the gallery of special-function tests, known as the test-suite "specfun". We will probably want to do this locally at first. Also I honestly don't know exactly how to hook up a new Multiprecision backend to the specfun tests.  
  
So I'll ask John (@jzmaddock), very briefly in a few keywords, how do I hook up a new backend to specfun testing in CI? briefly.  
  
Cc: @sinandredemption

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2023-01-08 14:36:11 UTC  
> Updated_at: 2023-01-08 14:36:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1374851172  

> briefly in a few keywords, how do I hook up a new backend to specfun testing in CI?  
  
Hi @jzmaddock I think I'm seeing the pattern.  
  
I need to:  
  
- Pick up the test files in Jamfile for my particular group.  
- Define the setup in the appropriate location(s) in source code.  
- Define the test suite in Jamfile. Let's call it`specfun_cpp_double_double`.  
- Select my backend of choice.  
- And the run a CI job (or command-line job) for the new `specfun_cpp_double_double`-suite.  
  
I'll try in my fork first.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2023-01-08 15:07:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1374857879  

Yup, duplicate this rule: https://github.com/boostorg/multiprecision/blob/8de45037b62b3405139d73d7a14acf0831bf63b6/test/Jamfile.v2#L1472 and rename the tests that are generated to match your new types name, then add the rule invocation below to actually create the testing target like this: https://github.com/boostorg/multiprecision/blob/8de45037b62b3405139d73d7a14acf0831bf63b6/test/Jamfile.v2#L1521  
  
Then modify https://github.com/boostorg/multiprecision/blob/develop/test/math/setup.hpp to set up testing of the new type, again copy the float128 case is the easiest way for what you have.  
  
Finally, duplicate and modify this line: https://github.com/boostorg/multiprecision/blob/8de45037b62b3405139d73d7a14acf0831bf63b6/test/math/instances/Jamfile.v2#L73 to create the template instantiations of the special functions.  This shouldn't be necessary, but it speeds up compilation to do things this way.  
  
And I think/hope that's the lot.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2023-01-08 15:23:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1374861366  

> Yup, duplicate this rule: ...  
  
Copy that John (@jzmaddock). It looks like the struggle with VC 14.0 continues a tiny bit. And maybe some other lint. But I'm ready to give specfun for `cpp_double_double` a try. I expect some tolerances will fail and some compiler issues may crop up.  
  
I keep looking ahead to the future, but after all that (and I do expect success), then the adaption of _quad_-_float_ (from the original feature-request) should get a lot easier...  
  
Cc: @sinandredemption

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2023-01-08 15:26:51 UTC  
> Updated_at: 2023-01-08 15:27:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1374862007  

And what does all this mean in the practical sense.  
  
I actually think we have a fair shake (and i'd like to give it a fair try) to get `cpp_double_fp_backend<T>` into 1.82 (with docs, funcs and tests and CI).  
  
Then I think later in the year we can take a more coherent run at _quad_-_float_, maybe even after getting some of the field experience with _double_-_float_.  
  
Cc: @jzmaddock and @sinandredemption and @mborland

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2023-01-08 17:52:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1374890877  

Tolerances should be broadly in agreement with similar backends - so say cpp_bin_float_quad.  The special fun tests are actually very good at really hammering basic arithmetic and have uncovered more than a few bugs in the past, so if you see anything wildly out, I would suspect a bug somewhere.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2023-01-08 18:58:47 UTC  
> Updated_at: 2023-01-08 18:59:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1374903606  

> The special fun tests are actually very good at really hammering basic arithmetic and have uncovered more than a few bugs in the past, so if you see anything wildly out, I would suspect a bug somewhere.  
  
Indeed that's for sure John (@jzmaddock). I'm psyched on finding out how it goes. I guess there are still a few remaining problems in CI so I'll work through these prior to going for specfun.  
  
This is the first time I've tried to add an entire backend on my own. It's challenging but a great learning experience.  
  
Also to be clear, I'm only (we're only) going for `cpp_double_fp_backend` in this PR and (maybe) for 1.82. The quead stuff will come later.  
  
Cc: @sinandredemption

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2023-01-09 14:13:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1375685187  

This first step took a while and has now _gone_ _green_.  
But we are **not yet** ready to merge.  
  
We still need the following:  
1. Test and pass specfun (might take a while)  
2. Write and build small docs on `cpp_double_fp_backend<T>`.  
3. Review.  
  
I am happy with this first preliminary green milestone and will now turn to points 1 and 2, beginning with specfun.  
  
At first I'll prepare this in our GSoC fork.  
  
Cc: @jzmaddock and @sinandredemption and @cosurgi and @mborland

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2023-01-10 11:10:25 UTC  
> Updated_at: 2023-01-10 11:11:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1377092658  

Hi John (@jzmaddock),  
  
I wonder if you could help just a tiny bit with guidance? Much to my delight my local tests of `specfun` running on `g++-9`, `-std=c++17` were remarkably successful on a first run. I received the following last lines of a build log...  
  
I got `...updated 337 targets...` but also `...failed updating 12 targets...` (see second text block below).  
  
Now I need to see the failures.  
  
From these failures on `Zeta`,  do you have any guidance on how I can actually run the relevant test case and put it under a microscopic debug session? I feel like I do not know what the `row` or `mean` are all about? See first text block below:  
  
For `Zeta`  
  
```sh  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Tests run with GNU C++ version 9.3.0, GNU libstdc++ version 20200808, linux  
Zeta: Random values greater than 1 with type cpp_double_double  
zeta<cpp_double_double> Max = 0.1958 RMS Mean=0.0706  
    worst case at row: 12  
    { 4.988127231597900390625, 1.037268683773212337925891820825986369163 }  
  
  
Zeta: Random values less than 1 with type cpp_double_double  
zeta<cpp_double_double> Max = 147.7 RMS Mean=13.23  
    worst case at row: 69  
    { -17.99382781982421875, -0.08425805633923885570999830270532256204479 }  
  
  
Zeta: Values close to and greater than 1 with type cpp_double_double  
zeta<cpp_double_double> Max = 0.19 RMS Mean=0.08633  
    worst case at row: 22  
    { 1.49527740478515625, 2.631125733364186076058384433696635480314 }  
  
  
Zeta: Values close to and less than 1 with type cpp_double_double  
zeta<cpp_double_double> Max = 1.003e+04 RMS Mean=2735  
    worst case at row: 16  
    { 0.999987542629241943359375, -80273.18355079243243774478653269383556504 }  
Peak error greater than expected value of 1000  
../../../libs/math/test/handle_test_result.hpp(165): [1;31;49merror: in "test_main": check bounds.first >= max_error_found has failed[0;39;49m  
Mean error greater than expected value of 200  
../../../libs/math/test/handle_test_result.hpp(170): [1;31;49merror: in "test_main": check bounds.second >= mean_error_found has failed[0;39;49m  
  
  
Zeta: Integer arguments with type cpp_double_double  
zeta<cpp_double_double> Max = 8.453 RMS Mean=2.34  
    worst case at row: 54  
    { 56.0, 1.0000000000000000138777878097252328 }  
  
  
  
[1;31;49m*** 2 failures are detected in the test module "Master Test Suite"  
[0;39;49m  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
Build result:  
  
```sh  
...failed updating 12 targets...  
...skipped 12 targets...  
...updated 337 targets...  
```  
  
Now I'm having difficulty identifying exactly which lines/cases/tests are needing special attention.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2023-01-10 12:15:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1377173589  

So if we look at:  
  
```  
Zeta: Values close to and less than 1 with type cpp_double_double  
zeta<cpp_double_double> Max = 1.003e+04 RMS Mean=2735  
    worst case at row: 16  
    { 0.999987542629241943359375, -80273.18355079243243774478653269383556504 }  
Peak error greater than expected value of 1000  
```  
  
The case causing the failure is `zeta(0.999987542629241943359375)` which should yield -80273.18355079243243774478653269383556504 but is 10^4eps out compared to an expected tolerance of 10^3.  Looking at the expected results for other types, this does indeed look like a _slight_ outlier.  
  
That case should go through:  
  
```  
template <class T, class Policy>  
T zeta_imp_prec(T s, T sc, const Policy&, const std::integral_constant<int, 113>&)  
```  
  
which is a rational approximation and should be super accurate - maybe check that the constant coefficients are being initialized correctly?  For comparison, maybe check the error rate for cpp_bin_float_quad.  
  
BTW I've been meaning to create a "dual precision" backend for multiprecision that would let you run the same calculation on two backends simultaneously and see where they they start to differ.

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2023-01-10 13:03:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1377226373  

Just tried:  
  
```  
   using mp = boost::multiprecision::number<boost::multiprecision::cpp_bin_float<106, boost::multiprecision::digit_base_2> >;  
   mp q("0.999987542629241943359375");  
   auto z = boost::math::zeta(q);  
  
   std::cout << boost::math::epsilon_difference(z, mp("-80273.18355079243243774478653269383556504")) << std::endl;  
```  
  
I get an error of 0, which looks good to me, so the difference with the double double looks like it might be real?

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2023-01-10 13:09:39 UTC  
> Updated_at: 2023-01-10 13:10:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1377234087  

> Just tried:  
> get an error of 0, which looks good to me, so the difference with the double double looks like it might be real?  
  
Indeed. I stepped through the impl. Your exact result makes sense to me. It especially makes sense, now that I study the implementation of `zeta()` in that region, which is solely based on algebraic polynomial expansion.  
  
I fear this might take us all the way back to some fundamental bit-losses in the basic operations add/sub/mul/div in the low-level implementation of `cpp_double_fp_backend<>`.  
  
We have an [open issue](https://github.com/BoostGSoC21/multiprecision/issues/35) (one of the first issues from July 2021) in the GSoC about this.  
  
I fear that this type (its low-level eval_ops might not be _fully_ _cooked_ yet.  
  
Cc: @sinandredemption This is something we still need to fully understand.

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2023-01-10 14:45:44 UTC  
> Updated_at: 2023-01-10 14:47:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1377387204  

> get an error of 0, which looks good to me  
  
Ahhh. OK, John (@jzmaddock), well, I figured out at least _partly_ what's going on, but I don't know the best way to fix it entirely in the backend/test/etc. world.  
  
- The input argument is an exact `double`. However the initialization of the argument is carried out with string-conversion, since we are above 64 digits, yet below 113 digits in the type.  
- This causes imprecision in the digits immediately following the `...375` in the input.  
- From that point on, the calculatoin carries this fuzz through.  
  
If I simply use the `double` representation of the number to construct the argument `x` to `zeta(x)`, then the input and following calculation retain exactness.  
  
I'll need to see if the backend (on its own) can handle the case decision to initialize with the native floating-point type OR string. I think I might need more constructors. Or if the backend can't handle this alone, then we need to think harder.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2023-01-10 16:53:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1377562689  

Is that not an error in string conversion then: I would assume it should convert to an exact binary value?

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2023-01-10 17:57:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1377642299  

> Is that not an error in string conversion then: I would assume it should convert to an exact binary value?  
  
I suppose maybe. That's what I was thinking and I do hope to find a solution there.  
  
I went (lazily) directly to `boost::multiprecision::detail::convert_from_string()`. But there are many places where I suppose I could have an error. Will investigate.

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2023-01-11 18:43:29 UTC  
> Updated_at: 2023-01-11 18:44:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1379330986  

>> Is that not an error in string conversion then: I would assume it should convert to an exact binary value?  
  
> hope to find a solution  
  
Hi John (@jzmaddock) regarding string input for `cpp_double_fp_backend`, after a while of thinking it's clear that you already solved this problem with `cpp_bin_float<>`. I racked my brain and found that for a binary backend, that's the working model. We need to (basically just like a compiler) support another multiprecision to fully interpret multiprecision strings.  
  
This will pull `cpp_int` into the game. But I'm OK with that.  
  
Along the lines of getting it working and then optimizing it, I find a usage of the `cpp_bin_float`-way for string-input is what is needed. Anything else does not capture the digits. And... Why should I write a different one when your code (adapted) will work for both of us?  
  
I do not know if my ramblings make sense. But If so, I'd duplicate your code, test it, then with you together, find a way to make it _publicly_ available for binary backends.  
  
Thoughts? Am I crazy?

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2023-01-12 11:50:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1380212863  

> Thoughts? Am I crazy?  
  
Probably crazy to want to take on that much coding, but yes, I fear that is the correct way to go.  The hard part is probably packing the bits in the resultant integers into the float type.

---

## Comment 26

> Username: sinandredemption  
> Created_at: 2023-01-12 16:46:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1380706539  

> Probably crazy to want to take on that much coding  
  
@ckormanyos Please do let me know in chats if you need an extra hand. I've been trying to start contributing again.

---

## Comment 27

> Username: cosurgi  
> Created_at: 2023-01-12 16:57:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1380720147  

It is great to see you going forward with this. Unfortunately I cannot join right now: too much work at the university at the moment :( But I will keep monitoring your changes, hoping to jump in when it becomes possible.

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2023-01-12 20:57:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1380986243  

>> you already solved this problem with cpp_bin_float<>  
  
> want to take on that much coding, but yes, I fear that is the correct way to go  
  
Thanks for your input John. Yes. I will try - based on Your code that is already fully tested, well-established and cleanly available in `cpp_bin_float`.  
  
This will have a bit of coding depth to it, so best to hammer this particular detail in our fork.

---

## Comment 29

> Username: ckormanyos  
> Created_at: 2023-01-12 21:01:29 UTC  
> Updated_at: 2023-01-12 21:02:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-1380989325  

> let me know in chats if you need an extra hand.  
  
Hi Fahad (@sinandredemption) we'll be moving back and forth to/from our GSoC fork for a couple of the more in-depth corrections.  
  
This PR has already shown that:  
  
- `cpp_double_fp_backend` seems robust enough to become a reliable badkend.  
- We still need to work on exact string read.  
- I'd like to study if we do/do-not have optimal algorithms for add/sub/mul/div/sqrt.  
- Passing all of `specfun` still needs a bunch of work locally like in our fork.  
  
I am going to start the first ponit back in our fork. I'll be posting more details on the above-mentioned issues of the fork. We can work out the heavy details there, and we can keep this PR open as we progress.

---

## Comment 30

> Username: codecov[bot]  
> Created_at: 2025-04-25 19:32:24 UTC  
> Updated_at: 2025-06-26 05:17:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2831276893  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.14050%` with `27 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.6%. Comparing base [(`2f87bf2`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/2f87bf2ddf05ce871499d37657063e15bdf1aa0e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5bfa551`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/5bfa55108a10478d6aa0d28a6a7c8ec7f74232cb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [test/reduced\_cpp\_double\_fp.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=test%2Freduced_cpp_double_fp.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9yZWR1Y2VkX2NwcF9kb3VibGVfZnAuY3Bw) | 75.0% | [15 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | 98.7% | [10 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/multiprecision/float128.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Ffloat128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9mbG9hdDEyOC5ocHA=) | 0.0% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [test/test\_round.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=test%2Ftest_round.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3JvdW5kLmNwcA==) | 95.0% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/515/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #515     +/-   ##  
=========================================  
- Coverage     94.1%   93.6%   -0.4%       
=========================================  
  Files          280     256     -24       
  Lines        29074   27847   -1227       
=========================================  
- Hits         27347   26058   -1289       
- Misses        1727    1789     +62       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...oost/multiprecision/cpp\_df\_qf/cpp\_df\_qf\_detail.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbC5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...ecision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_fabs.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_fabs.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZmFicy5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_floor.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_floor.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZmxvb3IuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...recision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_fma.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_fma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZm1hLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_frexp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_frexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZnJleHAuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_isinf.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_isinf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfaXNpbmYuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_isnan.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_isnan.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfaXNuYW4uaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_ldexp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_ldexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfbGRleHAuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...recision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_log.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_log.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfbG9nLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...ecision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_sqrt.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_sqrt.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfc3FydC5ocHA=) | `100.0% <100.0%> (ø)` | |  
| ... and [28 more](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [62 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/515/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2f87bf2...5bfa551](https://app.codecov.io/gh/boostorg/multiprecision/pull/515?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2025-06-20 18:30:41 UTC  
> Updated_at: 2025-06-20 18:32:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2992479252  

OK we are back to green with a known failure in CI-Inspect seemingly uinrelated to Multiprecision.  
  
The final issue is in edge-cases of the `modf` function (which may ultimately have edge cases in add/sub/mul/div).  
  
See also [BoostGSoC21/multiprecision/issues/193](https://github.com/BoostGSoC21/multiprecision/issues/193).

---

## Comment 32

> Username: ckormanyos  
> Created_at: 2025-06-21 09:11:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2993477184  

Well, this one is getting close, but there still seems to be a slight problem with rounding discovered in association with `modf`.  
  
I think I need some advice. I'm losing 1-eps when computing and recombining the integral and fractional parts.  
  
- Is there any reason _why_ one might believe that integral/fractional calculation and recombination might lose 1-eps?  
- Or might it rather be, as I sadly suspect, that there could be an underlying 1-eps problem in one or some of add/sub/mul/div?  
- Or is the double-float type lossy by nature? I thought (naively) it was as precise as its underlying components.  
  
I put a [workaround](https://github.com/boostorg/multiprecision/blob/f6ca99d93a372b48098e07a7ffa06601f8c2f4b5/test/test_round.cpp#L185) in `test_round.cpp`. For the `cpp_double_fp_backend` class there is a problem when computing and recombining the integral and fractional parts of floating-point. It is about $1/1000$ or so, so a kind of edge case. But I have not found the pattern when a candidate needs 1-eps.  
  
All the other types being tested have equality on the sum of the fractional part and integral part being equal to the original number. But I need `epsilon()` for the `cpp_double_fp_backend` case. Every other test in the Multiprecision suite (even specfun) is now going green.  
  
Cc: @jzmaddock and @cosurgi and @sinandredemption and @mborland

---

## Comment 33

> Username: ckormanyos  
> Created_at: 2025-06-22 11:27:27 UTC  
> Updated_at: 2025-06-22 11:39:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2994135600  

While contemplating this PR and the seemingly last issue on accuracy, I was reminded again of _Tight and Rigorous Error Bounds for Basic Building Blocks of Double-Word Arithmetic_ from M. M. Joldes, J. M. Muller and  V. Popescu.  
  
I have decided to refactor add/sub/mul/div according to the algorithms in that work and the clear advice offered in the Conclusions of Sect. 8.  
  
See also [JuliaMath/DoubleFloats.jl](https://github.com/JuliaMath/DoubleFloats.jl) and [esa-tu-darmstadt/twofloat](https://github.com/esa-tu-darmstadt/twofloat), where the latter actually has the exact algorithms implemented in the header [libtwofloat/arithmetics/double-word-arithmetic.hpp](https://github.com/esa-tu-darmstadt/twofloat/blob/main/include/libtwofloat/arithmetics/double-word-arithmetic.hpp).

---

## Comment 34

> Username: cosurgi  
> Created_at: 2025-06-22 12:17:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2994159856  

I hope that this will fix this last problem. Thank you so much for working on this.

---

## Comment 35

> Username: ckormanyos  
> Created_at: 2025-06-22 13:27:56 UTC  
> Updated_at: 2025-06-22 13:28:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2994215531  

> I hope that this will fix this last problem. Thank you so much for working on this.  
  
Thanks @cosurgi. I'm excited to code the algorithms exactly from the literature. I/we had been using some forms still based on the original Briggs work. I expect this perhaps final revision will bring the algorithms to the most modern point available.  
  
In general, I'm starting to believe that there is expected to be a little bit of _play_ in the algorithms for double-double. We are basically pasting two floating-points entities together, each having $1/2-ulp$ of roundoff-error.  
  
I will also keep a watchful eye on the runtime performance since we finally got the gain we need. Now we do not want to lose it unexpectedly.

---

## Comment 36

> Username: ckormanyos  
> Created_at: 2025-06-23 17:11:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2997243647  

> I'm excited to code the algorithms exactly from the literature. I/we had been using some forms still based on the original Briggs work.   
  
The algorithms already used have relatively good performance and subjectively tight error bounds. Addition and subtraction use those found in _Tight and Rigourous Error Bounds ..._, while multiplication and division use algorithms originally associated with Briggs' `doubledouble` and Shoup's `quad_float` in the NTL library.  
  
Briggs got it right in the first place. Shoup kept it right. And in this work, I added full edge-case correctness (inf, NaN, etc.). I suppose time will tell and I'll probably modify the algorithms or limits on the go later. But I'm fundamentally satisfied with the current state.

---

## Comment 37

> Username: ckormanyos  
> Created_at: 2025-06-23 17:13:53 UTC  
> Updated_at: 2025-06-23 17:32:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2997249163  

So folks, I have a few more issues in coverage and documentation ongoing. But the development drive on `cpp_double_fp_backend` has for the most part concluded. Please see also the rather long log in [BoostGSoC21/multiprecision/issues/160](https://github.com/BoostGSoC21/multiprecision/issues/160).  
  
Thanks @sinandredemption and @cosurgi for contributing to GSoC 21. I think this one is nearing Boost.  
  
The time for reflection and reviewing is upon us.  
  
I will be moving this forward for Boost soon. I'm not sure if 1.89 is the right call. I think 1.90 is a better fit. But if we get agreement soon, we can push for 1.89.  
  
So please if you get a chance, take a look at this work, review it critically. Contribute comments or doubts.  
  
Cc: @jzmaddock and @sinandredemption and @cosurgi and @mborland

---

## Comment 38

> Username: ckormanyos  
> Created_at: 2025-06-24 05:11:05 UTC  
> Updated_at: 2025-06-24 05:13:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-2998835189  

So it looks like I need a bit of help with CI. When I _thought_ I corrected some details in the main test `Jamfile` it seems like two small errors crept into CI.  
  
- One error involves MacOS systems. There is some kind of an issue with `0x7FFFFFFFFFFFFFFF` versus `0x8000000000000000`. This might come from `long` or `long long` conversion weaknesses in the code. But I believe none of the other systems have this error. I don't have these systems, so it is hard for me to track this error. It is in `test_round.cpp`.  
- The second error is in `test_legendre.cpp` which seems to want to pull in quadmath functions (for `cpp_bin_float::assign_float`). But it is doing so _without_ the presence of `libquadmath`. Now the `cpp_double_fp_backend` does in fact use an intermediate `cpp_bin_float` for reading strings. Maybe I just got the configuration wrong for _specfun_ in Jam.  
  
These are the two issues we discussed in other chats @mborland so if you get a chance, you might take a look.  
  
I probably just don't know what I'm doing in Jam, as usual.  
  
Cc: @jzmaddock

---

## Comment 39

> Username: mborland  
> Created_at: 2025-06-24 12:21:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3000147263  

Here is my local output from test round since I can replicate the error:  
  
```  
...  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
Error at this spot hint: what is going on here?  
test_round.cpp(97): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<!boost::multiprecision::is_interval_number<T>::value>::type check_within_half(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long]'  
Values were: 9223372036854775807                     9223372036854775806  
test_round.cpp(153): Rounded result differed by more than 1 from the original in function 'void check_trunc_result(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long]'  
Values were: 9223372036854775807                     9223372036854775806  
test_round.cpp(97): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<!boost::multiprecision::is_interval_number<T>::value>::type check_within_half(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long]'  
Values were: 9223372036854775806                     9223372036854775805  
test_round.cpp(153): Rounded result differed by more than 1 from the original in function 'void check_trunc_result(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long]'  
Values were: 9223372036854775806                     9223372036854775805  
test_round.cpp(97): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<!boost::multiprecision::is_interval_number<T>::value>::type check_within_half(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long long]'  
Values were: 9223372036854775807                     9223372036854775806  
test_round.cpp(153): Rounded result differed by more than 1 from the original in function 'void check_trunc_result(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long long]'  
Values were: 9223372036854775807                     9223372036854775806  
test_round.cpp(97): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<!boost::multiprecision::is_interval_number<T>::value>::type check_within_half(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long long]'  
Values were: 9223372036854775806                     9223372036854775805  
test_round.cpp(153): Rounded result differed by more than 1 from the original in function 'void check_trunc_result(T, U) [T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<long double>, boost::multiprecision::et_off>, U = long long]'  
Values were: 9223372036854775806                     9223372036854775805  
16 errors detected.  
  
EXIT STATUS: 16  
====== END OUTPUT ======  
```

---

## Comment 40

> Username: mborland  
> Created_at: 2025-06-24 13:54:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3000603395  

Ubuntu-jammy-specfun seems fine on Github Actions now. I added linking of quad math to your rule defining tests. Still trying to figure out what's up with rounding on Mac.

---

## Review 41 [Commented]

> Username: mborland  
> Created_at: 2025-06-24 15:08:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/515#pullrequestreview-2953685290  

📁 include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail_ccmath_floor.hpp

```diff
  20 |+ {
  21 |+    // Default to the regular floor function.
  22 |+    using std::floor;
```

> Username: mborland  
> Created_at: 2025-06-24 12:48:07 UTC  
> Updated_at: 2025-06-24 15:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2163909279  

This is only constexpr from C++23


📁 include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail.hpp

```diff
  11 |+ #define BOOST_MP_CPP_DF_QF_DETAIL_2023_01_02_HPP
  12 |+ 
  13 |+ #include <boost/config.hpp>
```

> Username: mborland  
> Created_at: 2025-06-24 13:03:44 UTC  
> Updated_at: 2025-06-24 15:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2163952498  

This won't work in standalone mode. We'll have to see what is being used from config and convert it from `BOOST_` to `BOOST_MP_`. I think already most things are in `<boost/multiprecision/standalone_config.hpp>`

> Username: ckormanyos  
> Created_at: 2025-06-24 18:02:16 UTC  
> Updated_at: 2025-06-24 18:02:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2164602155  

I will need a tad of help on this one, Matt. But that's also down the road a few days.


📁 include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail_ccmath_frexp.hpp

```diff
  23 |+    using std::frexp;
  24 |+ 
  25 |+    return frexp(arg, expptr);
```

> Username: mborland  
> Created_at: 2025-06-24 13:05:59 UTC  
> Updated_at: 2025-06-24 15:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2163956830  

This is only constexpr from C++23

> Username: ckormanyos  
> Created_at: 2025-06-24 18:01:45 UTC  
> Updated_at: 2025-06-24 18:01:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2164601258  

Thanks Matt,  
  
I'm aware of this. I need to do something akin to what you did in Boost.Math's `ccmath` work.  
  
But for now, I am going to kick these (and all of the similar ones) down the road a bit. I need to get the formal testing and correctness done first.  
  
I'd like to pick up this discussion later. I don't know if you ever did or didn't get all this to work out in `ccmath` yet?

> Username: mborland  
> Created_at: 2025-06-24 18:19:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2164630562  

Everything in ccmath should be working for the C++23 coverage. I haven't done the C++26 portions like log yet. Those got merged into the draft standard recently.


📁 include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail_ccmath_isinf.hpp

```diff
  17 |+ {
  18 |+    return (   ( x == cpp_df_qf_detail::ccmath::numeric_limits<FloatingPointType>::infinity())
  19 |+            || (-x == cpp_df_qf_detail::ccmath::numeric_limits<FloatingPointType>::infinity()));
```

> Username: mborland  
> Created_at: 2025-06-24 13:07:14 UTC  
> Updated_at: 2025-06-24 15:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2163959384  

I think for Intel platforms you need `x >= max` instead of equal to infinity to work with their fast math mode


📁 include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail_ccmath_ldexp.hpp

```diff
  22 |+    using std::ldexp;
  23 |+ 
  24 |+    return ldexp(arg, expval);
```

> Username: mborland  
> Created_at: 2025-06-24 13:07:43 UTC  
> Updated_at: 2025-06-24 15:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2163960346  

This is only constexpr from C++23


📁 include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail_ccmath_log.hpp

```diff
  22 |+    using std::log;
  23 |+ 
  24 |+    return log(x);
```

> Username: mborland  
> Created_at: 2025-06-24 13:08:39 UTC  
> Updated_at: 2025-06-24 15:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2163962234  

This is only constexpr from C++26


📁 include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail_ccmath_sqrt.hpp

```diff
  22 |+    using std::sqrt;
  23 |+ 
  24 |+    return sqrt(x);
```

> Username: mborland  
> Created_at: 2025-06-24 13:09:04 UTC  
> Updated_at: 2025-06-24 15:08:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#discussion_r2163963094  

This is only constexpr from C++26


---

## Comment 42

> Username: mborland  
> Created_at: 2025-06-24 15:15:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3000918866  

These cancelled runners for brownout are fixed on develop. There are some conflicts where I tried to rebase so I aborted.

---

## Comment 43

> Username: ckormanyos  
> Created_at: 2025-06-24 17:59:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3001406931  

Thanks Matt.  
  
> These cancelled runners for brownout are fixed on develop. There are some conflicts where I tried to rebase so I aborted.  
  
Is it in `multiprecision.yml`? I can't see any big diff's between develop's and mine. I'm not sure how to update to windows-2022.

---

## Comment 44

> Username: mborland  
> Created_at: 2025-06-24 18:03:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3001418485  

> Thanks Matt.  
>   
>   
>   
> > These cancelled runners for brownout are fixed on develop. There are some conflicts where I tried to rebase so I aborted.  
>   
>   
>   
> Is it in `multiprecision.yml`?   
  
Yes  
  
> I can't see any big diff's between develop's and mine. I'm not sure how to update to windows-2022.  
  
MSVC 14.2 can't be upgraded. There's also some stuff moved into containers. If you copy and paste off of develop it should be fine. I think the conflicts were elsewhere.

---

## Comment 45

> Username: ckormanyos  
> Created_at: 2025-06-24 18:04:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3001419383  

> Ubuntu-jammy-specfun seems fine on Github Actions now. I added linking of quad math to your rule defining tests.  
  
For now, this is fine. But it is not _quite_ right. That test is not intended to need `-lquadmath`.  
  
Having said that, the test has run for years. Why is it needing quadmath now? I traced the error to a helper function in `cpp_bin_float`. But is this pulling in `__float128` now due to a minute error on my side? Or has it been hidden for years?

---

## Comment 46

> Username: ckormanyos  
> Created_at: 2025-06-24 18:06:11 UTC  
> Updated_at: 2025-06-24 18:07:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3001424034  

>  Still trying to figure out what's up with rounding on Mac.  
  
There are 5 or six versions of `eval_convert_to` and I can see how _ALL_ of them need to be reworked now based on your input.  
  
Thanks, many thanks Matt (@mborland).  
  
I have reworked the 1 `eval_convert_to` that seems to be causing grief and I'd like to see how it shakes out. Then I will rework all of them accordingly.

---

## Comment 47

> Username: ckormanyos  
> Created_at: 2025-06-24 18:09:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3001431573  

> If you copy and paste off of develop  
  
Copy and paste _what_ off of develop? I can't see any big diffs in our YAMLs? Or is it picking up runners from elsewhere?

---

## Comment 48

> Username: mborland  
> Created_at: 2025-06-24 18:17:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3001451361  

> > If you copy and paste off of develop  
>   
>   
>   
> Copy and paste _what_ off of develop? I can't see any big diffs in our YAMLs? Or is it picking up runners from elsewhere?  
  
Sorry I just looked and the multiprecision.yml hasn't been updated. I did math last week and thought I did this one too. I'll make the updates.

---

## Comment 49

> Username: mborland  
> Created_at: 2025-06-24 21:55:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3001997782  

> > > If you copy and paste off of develop  
> >   
> >   
> > Copy and paste _what_ off of develop? I can't see any big diffs in our YAMLs? Or is it picking up runners from elsewhere?  
>   
> Sorry I just looked and the multiprecision.yml hasn't been updated. I did math last week and thought I did this one too. I'll make the updates.  
  
CI will be fixed here: https://github.com/boostorg/multiprecision/pull/700

---

## Comment 50

> Username: mborland  
> Created_at: 2025-06-25 13:10:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3004719199  

@ckormanyos This most recent set of commits add the testing, and one change to get standalone mode working. Locally it seems to work fine.

---

## Comment 51

> Username: mborland  
> Created_at: 2025-06-25 15:49:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3005272630  

Now that my extended test along the max boundary is included in the Jamfile it looks like the rounding problem may not be a Mac thing, but an ARM64 thing: (https://drone.cpp.al/boostorg/multiprecision/1060/139/2)  
  
```  
====== BEGIN OUTPUT ======  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775806                     9223372036854775805  
reduced_cpp_double_fp.cpp(80): Rounded result differed by more than 1 from the original in function 'void check_trunc_result(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int]'  
Values were: 9223372036854775806                     9223372036854775805  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775805                     9223372036854775804  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775804                     9223372036854775803  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775803                     9223372036854775802  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775802                     9223372036854775801  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775801                     9223372036854775800  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775800                     9223372036854775799  
reduced_cpp_double_fp.cpp(24): Rounded result differed by more than 0.5 from the original in function 'typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type check_within_half(T, U) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_double_fp_backend<double>, boost::multiprecision::et_off>; U = long int; typename std::enable_if<(! boost::multiprecision::is_interval_number<T>::value)>::type = void]'  
Values were: 9223372036854775799                     9223372036854775798  
...  
```

---

## Comment 52

> Username: ckormanyos  
> Created_at: 2025-06-25 15:56:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3005307175  

Thanks for all this Matt.  
  
I did a bit more cleanup too.  
  
But I need to see what's going on with the standalone tests. I won't get to this until tomorrow though.

---

## Comment 53

> Username: ckormanyos  
> Created_at: 2025-06-25 15:57:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3005310141  

> looks like the rounding problem may not be a Mac thing, but an ARM64 thing  
  
That yould make the most sense actually. It is an interesting case. It seems like a bug maight be lurking around somewhere, wherever that might be.

---

## Comment 54

> Username: mborland  
> Created_at: 2025-06-25 18:31:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3005757803  

I applied the same Apple workaround to ARM64 and it seems to do the trick. There was a missing `constexpr` on the new `extract` function which caused a number of failures. Fingers crossed this should run Green

---

## Comment 55

> Username: ckormanyos  
> Created_at: 2025-06-25 19:50:06 UTC  
> Updated_at: 2025-06-25 19:50:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3005954434  

> I applied the same Apple workaround to ARM64 and it seems to do the trick.  
  
Oh wow. Yeah that is really nice how you did that. Thank you Matt.  
  
> There was a missing `constexpr` on the new `extract` function which caused a number of failures. Fingers crossed this should run Green.  
  
Uggghhh how could I forget that. My bad.  
  
Well I _could_ do some more fiddling around with some of the `eval_convert_to` functions. But I'm going to stop fiddling around soon and leave all that to the evolution of this thing.  
  
Let's see if we can  get green. Then I think it's time to start discussing `develop`.  
  
Cc: @jzmaddock and @cosurgi and @sinandredemption

---

## Comment 56

> Username: mborland  
> Created_at: 2025-06-25 20:00:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3005978838  

>  I applied the same Apple workaround to ARM64 and it seems to do the trick.  
>   
  
Turns out every non-x64 platform needs the same workaround. I added a comment that I think x87 math may be helping out the conversion of un-representable numbers like `double(std::numeric_limits<long long>::max())`.   
  
> Let's see if we can get green. Then I think it's time to start discussing `develop`.  
>   
  
I don't see any issue with it hitting `develop`. 1.89 is coming around in short order so if we merge into `develop` soon it could make it out for that release. We could add something in the release notes that says it's in beta, but then at least we could have it in other people's hands.

---

## Comment 57

> Username: mborland  
> Created_at: 2025-06-25 21:06:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3006148256  

Looks like this is green across the board excluding the known to be broken inspect. Coverage at 98.2% of the diff for the PR is impressive given the size.

---

## Comment 58

> Username: ckormanyos  
> Created_at: 2025-06-25 21:18:47 UTC  
> Updated_at: 2025-06-25 21:19:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3006178359  

> Looks like this is green across the board excluding the known to be broken inspect. Coverage at 98.2% of the diff for the PR is impressive given the size.  
  
Yes.  
  
I need to do one final action. I think I need to merge all this _BACK_ to the original GSoC repo. Then verify the merge manually with a merge tool.  
  
I would like the PR to come from the GSoC21 repo. Only then can the change log and commit history be preserved.  
  
I think I can handle this tomorrow. After that, it's full speed ahead on Double FP.  
  
- Thanks Fahad (@sinandredemption) and Janek (@cosurgi) for the amazing work in the GSoC and your help beyond that.  
- Thank you Matt (@mborland). Just a week working with you cleared up numerous issues.  
- And thanks John (@jzmaddock) for your guidance and patiently helping us and guiding us to this point.  
  
Sure this library part will evolve. But it's time to go to user-land with it.  
  
I'll do the proper merging and PR this from GSoC 21. I can handle this alone (I hope). I will need a couple days for this activity.

---

## Comment 59

> Username: ckormanyos  
> Created_at: 2025-06-26 05:17:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3007085495  

This PR did not preserve the full commit log from thee original GSoC project. So I have replaced this one with #701 which corrects this. If you have been following this PR, it is replaced by #710.  
  
Closing.

---

## Comment 60

> Username: cosurgi  
> Created_at: 2025-06-26 11:43:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3008194365  

Thank you so much. I has been a pleasure working with you. It's awesome that you worked on it, so that it can be merged.

---

## Comment 61

> Username: ckormanyos  
> Created_at: 2025-06-26 12:19:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/515#issuecomment-3008293311  

> I has been a pleasure working with you.  
  
You to Janek. I think this thing will evolve a bit when it goes live. So my plan is to see how that shapes up for a while, what we learn.  
  
And after a learning period, we could go after `cpp_quad_fp_backend`, which will have its own set of challenges, but be a lot easier after learning so much from the double-word case.

---
