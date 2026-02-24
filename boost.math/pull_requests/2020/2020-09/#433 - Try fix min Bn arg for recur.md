# #433 Try fix min Bn arg for recur. [Merged]

> Username: ckormanyos  
> Created at: 2020-09-06 13:55:11 UTC  
> Updated at: 2021-01-06 14:31:32 UTC  
> Merged at: 2020-12-24 18:35:46 UTC  
> Closed at: 2020-12-24 18:35:46 UTC  
> Url: https://github.com/boostorg/math/pull/433  

Try first fix of divide by zero in `minimum_argument_for_bernoulli_recursion`

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2020-09-06 18:58:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/433#pullrequestreview-483165794  

📁 include/boost/math/special_functions/gamma.hpp

```diff
 382 | 
 373 |-    const float limit = std::ceil(std::pow(1.0f / std::ldexp(1.0f, 1-boost::math::tools::digits<T>()), 1.0f / 20.0f));
 383 |+    const float limit = ceil(pow(1.0f / ldexp(1.0f, argn_of_ldexp), 1.0f / 20.0f));
```

> Username: jzmaddock  
> Created_at: 2020-09-06 18:58:27 UTC  
> Updated_at: 2020-12-19 19:38:05 UTC  
> Url: https://github.com/boostorg/math/pull/433#discussion_r484103157  

Is this correct?  Haven't you changed binary bits: `tools::digits<T>()` to decimal digits?


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-09-06 19:12:45 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-687873942  

@ckormanyos thanks for looking at this, and sorry for being late to the party!  
  
If you remove the `f` suffixes from the literals then everything works for the test case given.  However, this just pushes the issue further into the long grass - the problem is underflow in ldexp.  
  
We could compute the limit at the precision of T using `ceil(pow(1/tools::epsilon<T>(), T(1)/20))`, but it's a much more expensive way of doing things.  Or, how about removing the `f` suffixes and then if `-tools::digits<T>() < std::numeric_limits<double>::min_exponent` assume that the value placed in `limit` will NOT be used and return `digits10_of_type * 1.7F`.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2020-09-06 20:33:58 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-687894397  

> We could compute the limit at the precision of T using ceil(pow(1/tools::epsilon<T>(), T(1)/20)), but it's a much more expensive way of doing things.  
  
Yeah. That pow requires exp and log and, you're right, that's something I think we would prefer to avoid.  
  
I wonder if it's possible to work out an asymptotic approximation and pick a few leading coefficients from there? It seems like we just need a simple upper limit of recursion. We have the ceiling of the power function of (one over a small number). I can also go through my notes from the past. I will look into this.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2020-09-06 20:55:40 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-687899292  

> We have the ceiling of the power function of (one over a small number)  
  
I am wondering about the leading term of 2^[(digits10 - 1)/20] if this can be estimated or limited to an upper bound in a simple way. But i am not quite sure exactly if that is the right interpretation of the pow and ldexp in this case.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2020-09-08 20:51:37 UTC  
> Updated_at: 2020-09-08 20:57:24 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-689129636  

The original line:  
  
```  
const float limit = std::ceil(std::pow(1.0f / std::ldexp(1.0f, 1-boost::math::tools::digits<T>()), 1.0f / 20.0f));  
```  
  
... has been replaced with an integer approximation. This avoids potential _overflow of the underflow_ from the division by `ldexp`.  
  
The `min` function is still used to differentiate between the factor 1.7*digits10 and the small-digits approximation. This is implemented in the new [body of the function](https://github.com/boostorg/math/blob/cb2bab6fd1b7d2fb4639b3d82d357d6b889208a3/include/boost/math/special_functions/gamma.hpp#L367) for `minimum_argument_for_bernoulli_recursion()`.  
  
One question is if the power-of-two expression is ever used since this helper function is not actually instantiated for low digit counts. The answer is yes. For dec-float at 39 decimal digits, for instance, the power-of-two lines are called and function properly.  
  
Some tests emphasizing the use of the helper function `minimum_argument_for_bernoulli_recursion()` will be added as soon as i can get the chance.  
  
These tests might look something like this (with check of control value to be done).  
  
```  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
int main(void)  
{  
  using float_type_dec38 = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<38>>;  
  using float_type_dec39 = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<39>>;  
  using float_type_dec40 = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<40>>;  
  
  const float_type_dec38 x38 = float_type_dec38(1) / 3;  
  const float_type_dec39 x39 = float_type_dec39(1) / 3;  
  const float_type_dec40 x40 = float_type_dec40(1) / 3;  
  
  const float_type_dec38 y38 = tgamma(x38);  
  const float_type_dec39 y39 = tgamma(x39);  
  const float_type_dec40 y40 = tgamma(x40);  
  
  // 2.67893853470774763365569294097467764412868937795730  
  std::cout << std::setprecision(std::numeric_limits<float_type_dec38>::digits10) << y38 << std::endl;  
  std::cout << std::setprecision(std::numeric_limits<float_type_dec39>::digits10) << y39 << std::endl;  
  std::cout << std::setprecision(std::numeric_limits<float_type_dec40>::digits10) << y40 << std::endl;  
  
  return 0;  
}  
```

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2020-09-13 20:25:28 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-691720633  

> Some tests emphasizing the use of the helper function `minimum_argument_for_bernoulli_recursion()` will be added as soon as i can get the chance.  
  
I am relatively sure the fix is numerically correct.  
  
But, regarding adding a few tests...  
  
@jzmaddock I do not know where these tests fit in best, in math or multiprecision. I would like to test, for instance, tgamma(1/3) for `cpp_dec_float<39>`, `<40>` and `<41>`.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-09-15 18:08:07 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-692884180  

>  I do not know where these tests fit in best, in math or multiprecision. I would like to test, for instance, tgamma(1/3) for cpp_dec_float<39>, <40> and <41>.  
  
Here, where the code is.  We should check cpp_bin_float as well.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2020-09-15 18:15:00 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-692887660  

> Here, where the code is. We should check cpp_bin_float as well.  
  
Agreed. I will comply. But I still need the location for the new tests.  
  
@jzmaddock John, when you say "Here, where the code is..." could you please indicate more clearly where the link is to "Here" please. For some reason I can not find the link or don't know what "Here..." means. Or did I overlook it?

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2020-12-13 10:28:08 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-743984853  

> Here, where the code is. We should check cpp_bin_float as well.  
  
Does anyone know if this issue is open/closed/solved/non-relevant/forgotten?  
I lost track of this issue and would like to handle it in 2020 if still open.  
I apologize for losing track of this issue.  
  
Thanky, Chris

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2020-12-13 11:08:49 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-743990790  

No worries, yes still open, I forgot all about it as well :(  
  
By "here", I mean under libs/math/test.  Probably worth a new test case rather than modifying an old one?

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2020-12-13 12:52:15 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-744003376  

> No worries, yes still open, I forgot all about it as well  
  
Thank you.  
  
> By "here", I mean under libs/math/test. Probably worth a new test case rather than modifying an old one?  
  
Understood. I will atempt to comply to this and report when womething is available.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2020-12-13 12:52:40 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-744003421  

I removed the words _[CI SKIP]_ frm the title of this PR

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2020-12-13 14:01:34 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-744012194  

Proposed fix is running in CI now.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2020-12-13 19:35:40 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-744057118  

> Proposed fix is running in CI now.  
  
That first fix was insufficient. Again, I have underestimated the care that must be taken when modifying tgamma and related functions.  
  
The second attempted fix is now in CI.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2020-12-14 19:34:16 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-744662617  

OK so once again, I've underestimated the depth of the CI.  
Have now reverted the issue 396 test code to C++03 (without C++11 features) for older compilers in various build configs.  
Hve also added (or attempted to add) big object flags for MSVC and GCC-MinGW.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2020-12-16 10:05:38 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-746023391  

#396 required more care and greater depth of resolution than I had initially envisioned.  
  
In this revision of the PR, we have:  
  
- Separated the large test files into 3 individual files (_big-object_ for GCC/VC was insufficient)  
- Reworked the exact numerical fix of the Bn recursion limit, as this actually broke some `RealConcept` GCC tests  
- Have run the tests locally, but with modern compilers  
  
So after all these refinements of the fix, the CI has been started again.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2020-12-16 10:43:53 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-746077058  

We unfortunately restarted because of old MSVC internal compiler error.  
Did not actually catch that one prior to CI

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2020-12-16 11:48:13 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-746164744  

Hi @jzmaddock:  
  
Fixing #396 has been a struggle.  
  
I have what's thought to be a good fix in the tgamma header code.  
  
But the `cpp_bin_float` test case in the file `test_issue396_cpp_bin_float.cpp` for MSVC9 (VS2008) fails with internal compiler error 1001. The similar `cpp_dec_float` code in `test_issue396_cpp_bin_float.cpp` works with the old compiler.  
  
I do not have a VS2008 any more and wondered if you do. If so, do you see any way to work around 1001? Or is it possible or advisable to remove these test cases for the older VC compilers?  
  
We can wait and see what AppVeyor says about all the compilers...

---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2020-12-16 12:05:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/433#pullrequestreview-553621612  

📁 include/boost/math/special_functions/gamma.hpp

```diff
 370 |-                                       ? static_cast<float>(std::numeric_limits<T>::digits10)
 371 |-                                       : static_cast<float>(boost::math::tools::digits<T>() * 0.301F));
 369 |+    BOOST_CONSTEXPR_OR_CONST float digits10_of_type =
```

> Username: jzmaddock  
> Created_at: 2020-12-16 12:05:06 UTC  
> Updated_at: 2020-12-19 19:38:05 UTC  
> Url: https://github.com/boostorg/math/pull/433#discussion_r544244755  

Unfortunate point of pedantry: this can't be constexpr since `tools::digits<>()` can't (always) be constexpr: for example with types whose precision varies at runtime (mpfr_float for eg).

> Username: ckormanyos  
> Created_at: 2020-12-16 12:37:14 UTC  
> Updated_at: 2020-12-19 19:38:05 UTC  
> Url: https://github.com/boostorg/math/pull/433#discussion_r544264522  

Thanks. Correct.  
I will change accordingly.


---

## Comment 20

> Username: jzmaddock  
> Created_at: 2020-12-16 12:15:15 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-746199669  

I don't have VC9 either.  I think:  
  
1) disable the tests for VC9 and earlier.  
2) In the long run we can stop running tests for all pre-C++11 compilers, since we've advertised that we're dropping that from next year.  Not sure if VC9 is in that group, but I suspect it is.

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2020-12-16 12:42:20 UTC  
> Updated_at: 2020-12-16 12:43:18 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-746232857  

> disable the tests for VC9 and earlier.  
  
Thank you , John. Sage advice...  
  
One of the three new files (the one problematic for the old VC versions) `test_issue396_cpp_bin_float.cpp` also ended up with 1001 for VC10.  
  
I propose to wait for AppVeyor to run a bit farther (at least until VC14, which ran on my own desktop). Maybe also see if a few MinGW-GCCs run on AppVeyor.  
  
Then we can see what-is-what and disable or refactor accordingly.  
  
In the meantime, I will tend to your review comment. Hmmm... I wonder if that could be the trouble-maker itself?

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2020-12-17 07:45:02 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-747271994  

Hi @jzmaddock, could you please take a look at the messages below?  
  
Thanks for your help so far.  
  
I was, in fact, able to find a compromise for those old compilers and am happy to not have to exclude any tests.  
  
But an additional, unexpected problem has arisen in CI regarding `chebyshev_test.cpp`, although I can not at the moment quite comprehend why this issue's minute changed in Bernoulli numbers have a cross-influence in Chebyshev.  
  
So just on a _guess_, I added _forward_args_ to `chebyshev.hpp` and fired up CI yet again.  
  
What do you think?  
  
```  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(23): error C2039: 'promote_args': is not a member of 'boost::math::tools'  
[00:12:50] C:\projects\boost-root\boost/math/tools/convert_from_string.hpp(17): note: see declaration of 'boost::math::tools'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(23): error C2988: unrecognizable template declaration/definition  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(23): error C2143: syntax error: missing ';' before '<'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(23): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(23): error C2059: syntax error: '<'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(23): error C2039: 'type': is not a member of '`global namespace''  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(24): error C2143: syntax error: missing ';' before '{'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(24): error C2447: '{': missing function header (old-style formal list?)  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(91): error C2039: 'promote_args': is not a member of 'boost::math::tools'  
[00:12:50] C:\projects\boost-root\boost/math/tools/convert_from_string.hpp(17): note: see declaration of 'boost::math::tools'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(91): error C2988: unrecognizable template declaration/definition  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(91): error C2143: syntax error: missing ';' before '<'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(91): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(91): error C2374: 'boost::math::promote_args': redefinition; multiple initialization  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(23): note: see declaration of 'boost::math::promote_args'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(91): error C2059: syntax error: '<'  
[00:12:50] C:\projects\boost-root\boost/math/special_functions/chebyshev.hpp(91): error C2039: 'type': is not a member of '`global namespace''  
...  
...  
...  
[00:12:50] ...failed compile-c-c++ ..\..\..\bin.v2\libs\math\test\chebyshev_test.test\e93aca2a146c00a54b4caa122ad1c138\chebyshev_test.obj...  
[00:12:50] ...skipped <p..\..\..\bin.v2\libs\math\test\chebyshev_test.test\e93aca2a146c00a54b4caa122ad1c138>chebyshev_test.exe for lack of <p..\..\..\bin.v2\libs\math\test\chebyshev_test.test\e93aca2a146c00a54b4caa122ad1c138>chebyshev_test.obj...  
[00:12:50] ...skipped <p..\..\..\bin.v2\libs\math\test\chebyshev_test.test\e93aca2a146c00a54b4caa122ad1c138>chebyshev_test.run for lack of <p..\..\..\bin.v2\libs\math\test\chebyshev_test.test\e93aca2a146c00a54b4caa122ad1c138>chebyshev_test.exe...  
```

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2020-12-18 19:21:42 UTC  
> Updated_at: 2020-12-18 19:24:01 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-748275498  

Hi @jzmaddock: Could you please help explaining some of the CI failures in AppVeyor?  
  
So although I feel like the coding of this bug fix is generally coming along. In fact, the `special_fun` tests including my new tests are running on all compilers, as are quadrature and GCC's quad. So that's looking good.  
  
This is expected to be a small fix (although `tgamma()` is centrally fundamental).  
Nonetheless, there are some failures in CI on AppVeyor that I simply do not comprehend. It seems like the test suite _misc_ is causing problems on certain compilers.  
  
I can not exactly reproduce these failures on my local machine with VC14.2  
  
Some failures are like this, unable to link with `boost::math::isinf()`  
  
```  
[00:30:32] sf_bernoulli_incl_test.obj : error LNK2019: unresolved external symbol "bool __cdecl boost::math::isinf<long double>(long double)" (??$isinf@O@math@boost@@YA_NO@Z) referenced in function "public: void __cdecl boost::math::detail::bernoulli_numbers_cache<long double,struct boost::math::policies::policy<struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy> >::tangent(unsigned __int64)" (?tangent@?$bernoulli_numbers_cache@OU?$policy@Udefault_policy@policies@math@boost@@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@@policies@math@boost@@@detail@math@boost@@QEAAX_K@Z)  
[00:30:32] sf_bernoulli_incl_test.obj : error LNK2019: unresolved external symbol "bool __cdecl boost::math::isinf<double>(double)" (??$isinf@N@math@boost@@YA_NN@Z) referenced in function "public: void __cdecl boost::math::detail::bernoulli_numbers_cache<double,struct boost::math::policies::policy<struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy> >::tangent(unsigned __int64)" (?tangent@?$bernoulli_numbers_cache@NU?$policy@Udefault_policy@policies@math@boost@@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@@policies@math@boost@@@detail@math@boost@@QEAAX_K@Z)  
[00:30:32] sf_bernoulli_incl_test.obj : error LNK2019: unresolved external symbol "bool __cdecl boost::math::isinf<float>(float)" (??$isinf@M@math@boost@@YA_NM@Z) referenced in function "public: void __cdecl boost::math::detail::bernoulli_numbers_cache<float,struct boost::math::policies::policy<struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy> >::tangent(unsigned __int64)" (?tangent@?$bernoulli_numbers_cache@MU?$policy@Udefault_policy@policies@math@boost@@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@@policies@math@boost@@@detail@math@boost@@QEAAX_K@Z)  
```  
  
There are similar failures for `boost::math::sign()`.  
  
I could imagine including `<boost/math/special_functions/fpclassify.hpp>`. But it seems like this is not or not intuitively correlated to the minor changes in `gamma.hpp` in this branch.  
  
There are some other errors with some kind of stuff with lexical cast, but these are not intuitively easy to understand.  
  
Here is one of the [logs in question](https://ci.appveyor.com/project/jzmaddock/math/builds/36893994/job/b87hdvf7hhf9no6t).  
  
Thanks if you get a chance to look into these matters, John. I am kind of not able to move forwards and would really appreciate your help.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2020-12-19 11:10:53 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-748459276  

> Could you please help explaining some of the CI failures in AppVeyor?  
  
I'm running locally, but it may take a while! :(

---

## Comment 25

> Username: ckormanyos  
> Created_at: 2020-12-19 14:15:05 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-748480139  

> I'm running locally, but it may take a while!  
  
Thank you John.  
  
I started another CI run, but the problems in _misc_ remain present.  
  
One testing issue in `sf_bernoulli_incl_test.cpp` is particularly intriguing. The code is relatively straighforward. Although I'm not an expert in the origins of these tests, I start wondering if some kind of reliance on `main.cpp` in `compile_test` needs a build dependency or something. Also really confising to me is why new or seemingly new problems arise just due to my (thought-to-be) minimal changes in `gamma.hpp`.  
  
```  
[00:06:56] msvc.link ..\..\..\bin.v2\libs\math\test\sf_bernoulli_incl_test.test\75f50373b504f6f2e4c1c005da931bac\sf_bernoulli_incl_test.exe  
[00:06:56] sf_bernoulli_incl_test.obj : error LNK2019: unresolved external symbol "bool __cdecl boost::math::isinf<long double>(long double)" (??$isinf@O@math@boost@@YA_NO@Z) referenced in function "public: void __thiscall boost::math::detail::bernoulli_numbers_cache<long double,struct boost::math::policies::policy<struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy> >::tangent(unsigned int)" (?tangent@?$bernoulli_numbers_cache@OU?$policy@Udefault_policy@policies@math@boost@@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@@policies@math@boost@@@detail@math@boost@@QAEXI@Z)  
[00:06:56] sf_bernoulli_incl_test.obj : error LNK2019: unresolved external symbol "bool __cdecl boost::math::isinf<double>(double)" (??$isinf@N@math@boost@@YA_NN@Z) referenced in function "public: void __thiscall boost::math::detail::bernoulli_numbers_cache<double,struct boost::math::policies::policy<struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy> >::tangent(unsigned int)" (?tangent@?$bernoulli_numbers_cache@NU?$policy@Udefault_policy@policies@math@boost@@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@@policies@math@boost@@@detail@math@boost@@QAEXI@Z)  
[00:06:56] sf_bernoulli_incl_test.obj : error LNK2019: unresolved external symbol "bool __cdecl boost::math::isinf<float>(float)" (??$isinf@M@math@boost@@YA_NM@Z) referenced in function "public: void __thiscall boost::math::detail::bernoulli_numbers_cache<float,struct boost::math::policies::policy<struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy,struct boost::math::policies::default_policy> >::tangent(unsigned int)" (?tangent@?$bernoulli_numbers_cache@MU?$policy@Udefault_policy@policies@math@boost@@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@U1234@@policies@math@boost@@@detail@math@boost@@QAEXI@Z)  
```

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2020-12-19 18:12:24 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-748506713  

I see the problem, I'll fix in develop shortly.... more soon.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2020-12-19 19:39:10 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-748516146  

OK, the issue was that unrelated changes to lexical_cast.hpp exposed some missing #includes here.  Fixed in develop and then merged to this PR.  Fingers crossed!!!

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2020-12-19 21:24:12 UTC  
> Updated_at: 2020-12-19 21:25:52 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-748527352  

> OK, the issue was that unrelated changes to lexical_cast.hpp exposed some missing #includes here. Fixed in develop and then merged to this PR. Fingers crossed!!!  
  
Thank you, John! That makes sense.  
We'll watch the CI run.

---

## Comment 29

> Username: ckormanyos  
> Created_at: 2020-12-20 11:24:57 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-748594746  

So far, things in CI are moving along well. Thanks again, John.  
  
Here are some additional review comments regarding range-checking.  
  
Critical numerical scope of the changes in PR is: [this line](https://github.com/boostorg/math/blob/issue396/include/boost/math/special_functions/gamma.hpp#L392)  
  
- Check for low end zero with `digits10_of_type` equal to 0.0F, then we find `Exp[((0.0/0.301) Log[2.0])/20.0] = 1.0` and `min_arg` is 0. So OK.  
- Check low end non-zero with `digits10_of_type` equal to 0.301F, then we find `Exp[((0.301/0.301) Log[2.0])/20.0] = 1.03526.` and `min_arg` is 2. So OK.  
- Check low end non-zero with `digits10_of_type` equal to 49.99F, then we find `Exp[((49.99/0.301) Log[2.0])/20.0] = 316.045` and `min_arg` is `49.99 * 1.7`. So OK. Also in this case the argument of the `exp` function is about 5.6 so there is no overflow danger for built-in `float`.  
  
So these range checks seem to be all OK.

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2020-12-23 19:04:30 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-750432802  

Hi @jzmaddock AppVeyor OK. Travis has run but one job failed on the level of `sudo get...`. What is your take on this?

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2020-12-24 13:54:31 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-750889561  

I'll echo the words from another PR.  
  
@jzmaddock This should be good to go. The only CI failure was an error on Travis' end.

---

## Comment 32

> Username: jzmaddock  
> Created_at: 2020-12-24 18:35:38 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-750942595  

Thanks for seeing this through Chris!

---

## Comment 33

> Username: ckormanyos  
> Created_at: 2020-12-25 08:45:41 UTC  
> Url: https://github.com/boostorg/math/pull/433#issuecomment-751211072  

> Thanks for seeing this through  
  
Thanks also for your help. I learned quite a bit from this change request and this PR. The changes in `gamma.hpp` wer straightforward. Writing test code to exercise the multiprecision domain of the changes was, however, challenging --- especially trying to write some test code and try to empirically _guess_ if it would run on the older compilers (and the new ones).

---
