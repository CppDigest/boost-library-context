# #326 Cleanup more cpp dec float [Merged]

> Username: ckormanyos  
> Created at: 2021-05-19 15:59:55 UTC  
> Updated at: 2021-05-21 21:29:17 UTC  
> Merged at: 2021-05-21 20:51:38 UTC  
> Closed at: 2021-05-21 20:51:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326  

Restore hastily removed _big_ _Lanczos_ in `cpp_dec_float`.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-05-20 07:09:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-844775735  

This PR is also picking up corrections found/related to #325

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-05-20 10:10:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-844947454  

There are potential related sub-issues that I have discovered or re-realized. Sample test code below.  
  
@jzmaddock I have 4 sub-issues I'd like to clear up at your convenience...  
  
We find:  
1. Failures in CI for this PR at the moment are entirely related to clang 9 on GHA. We should probably react here as we did in math?  
2. The example below compiles and runs for `cpp_dec_float` when the change to re-include _big_ _Lanczos_ is done.  
3. When compiling, however, with `TEST_CPP_BIN_FLOAT`, the system has trouble instantiating `cpp_bin_float<10U>`. Higher digit counts OK. I must admit I have never instantiated bin float for such low digits. We should either `#error`or allow 10 digits.  
4. I was re-reminded, once again that _any_ numeric type being used with math's `tgamma` and friends functions must provide a specialization (for low digit counts) of `struct boost::math::lanczos::lancos {};`. Should this be/is this already well enough documented?  
  
```  
#define TEST_CPP_DEC_FLOAT  
//#define TEST_CPP_BIN_FLOAT  
  
#include <iomanip>  
#include <iostream>  
  
#if defined(TEST_CPP_DEC_FLOAT) && defined(TEST_CPP_BIN_FLOAT)  
#error Error: Only one of either TEST_CPP_DEC_FLOAT or TEST_CPP_BIN_FLOAT can be defined at once.  
#endif  
  
#if defined(TEST_CPP_DEC_FLOAT)  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#endif  
  
#if defined(TEST_CPP_BIN_FLOAT)  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#endif  
  
template<typename T>  
bool test_tgamma_small_digits()  
{  
   // N[Gamma[5/2], 120]  
   const T control_tgamma_2_and_half("1.32934038817913702047362561250585888709816209209179034616035584238968346344327413603121299255390849906217011771821192800");  
  
   // N[Gamma[5000/2], 120]  
   const T control_tgamma_2_fifty   ("1.29314250436364309292832582080974738839793748706951226669917697084512949902204448379552716614841127978037140294127577317E490");  
  
   const T tgamma_2_and_half = boost::math::tgamma(T(T(5)   / 2));  
   const T tgamma_2_fifty    = boost::math::tgamma(T(T(500) / 2));  
  
   const T closeness_2_and_half = fabs(1 - fabs(tgamma_2_and_half / control_tgamma_2_and_half));  
   const T closeness_2_fifty    = fabs(1 - fabs(tgamma_2_fifty    / control_tgamma_2_fifty));  
  
   const bool result_is_ok_2_and_half = (closeness_2_and_half < std::numeric_limits<T>::epsilon() * T(1.0E6));  
   const bool result_is_ok_2_fifty    = (closeness_2_fifty    < std::numeric_limits<T>::epsilon() * T(1.0E6));  
  
   const bool result_is_ok = (result_is_ok_2_and_half && result_is_ok_2_fifty);  
  
   return result_is_ok;  
}  
  
int main()  
{  
  #if defined(TEST_CPP_DEC_FLOAT)  
  using big_float_010_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float< 10>>;  
  using big_float_035_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float< 35>>;  
  using big_float_105_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<105>>;  
  #endif  
  
  #if defined(TEST_CPP_BIN_FLOAT)  
  using big_float_010_type = boost::multiprecision::number<boost::multiprecision::cpp_bin_float< 10>>;  
  using big_float_035_type = boost::multiprecision::number<boost::multiprecision::cpp_bin_float< 35>>;  
  using big_float_105_type = boost::multiprecision::number<boost::multiprecision::cpp_bin_float<105>>;  
  #endif  
  
  const bool result_010_is_ok = test_tgamma_small_digits<big_float_010_type>();  
  const bool result_035_is_ok = test_tgamma_small_digits<big_float_035_type>();  
  const bool result_105_is_ok = test_tgamma_small_digits<big_float_105_type>();  
  
  std::cout << "result_010_is_ok: " << std::boolalpha << result_010_is_ok << std::endl;  
  std::cout << "result_035_is_ok: " << std::boolalpha << result_035_is_ok << std::endl;  
  std::cout << "result_105_is_ok: " << std::boolalpha << result_105_is_ok << std::endl;  
}  
```

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-05-20 10:43:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-844972390  

> Failures in CI for this PR at the moment are entirely related to clang 9 on GHA. We should probably react here as we did in math?  
  
Nod, already done in https://github.com/boostorg/multiprecision/pull/318/commits/67dd85efd8fb13ef737888fa05a40c03b96cef32, I've just cherry-picked that to develop.  
  
> When compiling, however, with TEST_CPP_BIN_FLOAT, the system has trouble instantiating cpp_bin_float<10U>. Higher digit counts OK. I must admit I have never instantiated bin float for such low digits. We should either #erroror allow 10 digits.  
  
It works for mefor basic arithmetic, but fails inside tgamma on a comparison to a literal double (0.5).  This is an intentional unintended consequence ;)  The idea is that implicit conversion from type which would lose precision (and double to a 10-digit type would do so) should not be an implicit conversion.  It should fail for cpp_dec_float as well at that digit count.  
  
> I was re-reminded, once again that any numeric type being used with math's tgamma and friends functions must provide a specialization (for low digit counts) of struct boost::math::lanczos::lancos {};. Should this be/is this already well enough documented?  
  
I'm not sure that's the case: it is finding one of the default Lanczos implementations, and it *should* really work, I need to investigate some more.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-05-20 11:20:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-844997628  

>  not sure that's the case: it is finding one of the default Lanczos implementations, and it _should_ really work, I need to investigate some more.  
  
Thanks @jzmaddock I was hoping you would find, in particular, that issue to be peculiar.  
  
I prepared the following code four your investigation. There are comments with your name _John_ in the test below. There you can instantiate with 36 decimal digits or then 35 to see the cross-over to NOT OK for `cpp_bin_float` low digit `tgamma`.  
  
My suspicion is that something along the line is inadvertently converted to the precision og built-in `double`.  
  
```  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
template<typename T>  
bool test_tgamma_small_digits()  
{  
   // N[Gamma[5/2], 120]  
   const T control_tgamma_2_and_half("1.32934038817913702047362561250585888709816209209179034616035584238968346344327413603121299255390849906217011771821192800");  
  
   // N[Gamma[5000/2], 120]  
   const T control_tgamma_2_fifty   ("1.29314250436364309292832582080974738839793748706951226669917697084512949902204448379552716614841127978037140294127577317E490");  
  
   const T tgamma_2_and_half = boost::math::tgamma(T(T(5)   / 2));  
   const T tgamma_2_fifty    = boost::math::tgamma(T(T(500) / 2));  
  
   const T closeness_2_and_half = fabs(1 - fabs(tgamma_2_and_half / control_tgamma_2_and_half));  
   const T closeness_2_fifty    = fabs(1 - fabs(tgamma_2_fifty    / control_tgamma_2_fifty));  
  
   std::cout << "closeness_2_and_half: " << closeness_2_and_half << std::endl;  
   std::cout << "closeness_2_fifty   : " << closeness_2_fifty    << std::endl;  
  
   const bool result_is_ok_2_and_half = (closeness_2_and_half < std::numeric_limits<T>::epsilon() * T(1.0E6));  
   const bool result_is_ok_2_fifty    = (closeness_2_fifty    < std::numeric_limits<T>::epsilon() * T(1.0E6));  
  
   const bool result_is_ok = (result_is_ok_2_and_half && result_is_ok_2_fifty);  
  
   return result_is_ok;  
}  
  
int main()  
{  
  // John: Instantiate this with 36 decimal digits, where all is OK  
  // then instantiate with 35 decimal digits to find crossover to Not OK.  
  
  using big_float_type = boost::multiprecision::number<boost::multiprecision::cpp_bin_float<36>>;  
  //using big_float_type = boost::multiprecision::number<boost::multiprecision::cpp_bin_float<35>>;  
  
  const bool result_is_ok = test_tgamma_small_digits<big_float_type>();  
  
  std::cout << "result_is_ok: " << std::boolalpha << result_is_ok << std::endl;  
}  
```

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-05-20 12:23:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-845055310  

We have a bad (and I hope it's just the one!) lanczos trait - and it only kicked in for 34 and 35 digit counts so we were lucky to find this.  Removed in https://github.com/boostorg/math/pull/632.  
  
Once that's in develop, I'll increase testing here.  
  
You should also be able to ditch big_lanczos.hpp again as well ;)

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-05-20 12:28:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-845060625  

> have a bad (and I hope it's just the one!) lanczos trait - and it only kicked in for 34 and 35 digit counts so we were lucky to find this  
  
This is great. Many thanks, John! It is always amazing how subtle some of these hard-to-find parameter-range weaknesses can be. It was nice to find that one indeed.  
  
Awesome! We will stay in contact and put the right testing/removals of specializations, etc. in the right places.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-05-20 15:13:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-845209543  

Re tests, note that we already have https://github.com/boostorg/multiprecision/blob/develop/test/math/test_gamma.cpp, we just need to actually run these in our CI - they aren't at present because they take so long to run, but we could run *one* compiler combination on them I suspect.  
  
Also need to update the precisions tested (certainly for tgamma) to make sure we exercise each Lanczos approximation at either end of the digit range it's used over.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2021-05-20 15:25:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-845219674  

> Re tests, note that we already have https://github.com/boostorg/multiprecision/blob/develop/test/math/test_gamma.cpp, we just need to actually run these in our CI  
  
I understand. Perhaps I'm fumbling around putting in redundant, not-needed tests. We can remove at will.  
  
I see. Perhaps we need to negotiate the scope of that file and the numbers of test cases could maybe be reduced? Maybe just MSVC, a few GCC version(s). That test might also be (thinking ahead) a really good one as moving forward on QuadDouble.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-05-20 15:39:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-845230782  

Nod.  Yes, running those tests on QuadFloat moving forward would be good.  Those tests run the special function Boost.Math tests - so they do take a while, but they also really hammer the multiprecision code, which is very useful for catching bugs.  The main tests can only be run up to ~ 40 digit precision due to the limited range of Boost.Math's test data, these: https://github.com/boostorg/multiprecision/tree/develop/test/math/high_prec contain higher precision tests for tgamma/lgamma only, but are *really* slow.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-05-21 12:26:05 UTC  
> Updated_at: 2021-05-21 12:28:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-845914794  

Tests are now cycling for a few selected `tgamma`points on both dec/bin float, although these may be removed in the future in favor of more depth of testing from other Jamfile(s).  
  
Hi @jzmaddock I can now report:  
  
- A Boost style check error was found in the previous CI in a local definition of `max` in the test code. This has now been cleaned up.  
- For the moment, I decided to restore and retain `dec_float`'s (multiprecision-version) `#include` of _big_ _Lanczos_.  
- If this PR's CI passes, then I'll move forward on to merge to develop and we can straighten out the testing in more depth in other issues and PRs.  
- What we do with the skeleton `tgamma` tests in this PR remains open, and we can decide when we complete other testing refinement(s).

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-05-21 21:29:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/326#issuecomment-846272765  

Increased depth of testing in CI is being pursued in #327

---
