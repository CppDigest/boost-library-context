# #1251 Fix lower incomplete gamma functions with x = 0 [Merged]

> Username: cohomology  
> Created at: 2025-04-02 07:53:28 UTC  
> Updated at: 2025-05-23 17:27:56 UTC  
> Merged at: 2025-05-23 17:27:56 UTC  
> Closed at: 2025-05-23 17:27:56 UTC  
> Url: https://github.com/boostorg/math/pull/1251  

In this case, the errno error handling did not work correctly, as internal functions where accidently setting it, although no overflow happens.  
  
Fixes #1249.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-02 14:15:31 UTC  
> Updated_at: 2025-04-07 18:50:35 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2772708133  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1251?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.55072%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 93.82%. Comparing base [(`a5c0625`)](https://app.codecov.io/gh/boostorg/math/commit/a5c062542afb2037054c4e1b1be44db7b0859c38?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`7503be4`)](https://app.codecov.io/gh/boostorg/math/commit/7503be4cfaf38533fff0c6206b0b1461503743a0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1251?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [test/git\_issue\_1249.cpp](https://app.codecov.io/gh/boostorg/math/pull/1251?src=pr&el=tree&filepath=test%2Fgit_issue_1249.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTI0OS5jcHA=) | 98.38% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1251?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1251/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1251?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1251      +/-   ##  
===========================================  
- Coverage    93.83%   93.82%   -0.01%       
===========================================  
  Files          657      658       +1       
  Lines        55244    55330      +86       
===========================================  
+ Hits         51840    51916      +76       
- Misses        3404     3414      +10       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1251?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1251?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `92.08% <100.00%> (-0.18%)` | :arrow_down: |  
| [test/git\_issue\_1249.cpp](https://app.codecov.io/gh/boostorg/math/pull/1251?src=pr&el=tree&filepath=test%2Fgit_issue_1249.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTI0OS5jcHA=) | `98.38% <98.38%> (ø)` | |  
  
... and [7 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1251/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1251?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1251?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a5c0625...7503be4](https://app.codecov.io/gh/boostorg/math/pull/1251?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2025-04-17 17:55:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1251#pullrequestreview-2776539641  

📁 include/boost/math/special_functions/gamma.hpp

```diff
1633 |    }
1630 |-    if(p_derivative)
1634 |+    if(p_derivative && x > 0)
```

> Username: jzmaddock  
> Created_at: 2025-04-17 17:55:01 UTC  
> Updated_at: 2025-04-17 17:55:02 UTC  
> Url: https://github.com/boostorg/math/pull/1251#discussion_r2049420934  

I think we need to let this line execute without the check for `x > 0`, that way our root finder will get a derivative back: in this particular case, any arbitrary large value will do.... ah but there should be an `else` before the `*p_derivative /= x;`.

> Username: cohomology  
> Created_at: 2025-04-18 10:08:33 UTC  
> Url: https://github.com/boostorg/math/pull/1251#discussion_r2050447869  

Hi, I adjusted the coding. Is it okay now?   
  
I also corrected some wrong ternary operators in the file which prevent using some functions with non-standard float/double T template parameters.


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-04-17 17:58:20 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2813676581  

Other than my one small comment, this all looks good.  Many thanks for this!

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-04-18 10:28:31 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2815200595  

Looks great, thanks, just running CI now...

---

## Comment 5

> Username: cohomology  
> Created_at: 2025-04-18 16:08:10 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2815716582  

Where are these CI errors come from? I don't get them ...

---

## Comment 6

> Username: mborland  
> Created_at: 2025-04-18 16:41:31 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2815824430  

> Where are these CI errors come from? I don't get them ...  
  
The Cauchy one was failing with the SYCL compiler, and the beta ones are new failure there that are unrelated. The last one is we need to increment the current version in Boost.Config. I would not worry about any of these failures. I need to address them, and make sure we haven't broken CUDA along the way

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2025-04-18 17:37:08 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2815905353  

Ah shucks, sorry, I hadn't spotted that the new beta tests were failing on CUDA.  @mborland some of them are trivial failures (need to update expected error rates), but there are a few "gross" errors which *might* warrant investigation.  Or it may be that the inputs are so extreme that there's not much we can do on that platform.  If you have CUDA/Sycl set up can I let you investigate?

---

## Comment 8

> Username: mborland  
> Created_at: 2025-04-18 17:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2815911957  

> Ah shucks, sorry, I hadn't spotted that the new beta tests were failing on CUDA. @mborland some of them are trivial failures (need to update expected error rates), but there are a few "gross" errors which _might_ warrant investigation. Or it may be that the inputs are so extreme that there's not much we can do on that platform. If you have CUDA/Sycl set up can I let you investigate?  
  
Yes, next week I'll look into these. The Scipy guys also are in a position now to use the CUDA stuff so I'll put some effort into the rest of the library too.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2025-04-28 10:43:01 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2834828533  

@cohomology can you please merge develop into this PR so we kick off a fresh CI run and do a final check before merging?  Thanks!

---

## Comment 10

> Username: cohomology  
> Created_at: 2025-04-28 12:04:11 UTC  
> Updated_at: 2025-04-28 12:15:19 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2835023022  

I don't understand your changes in gamma.hpp.  
  
The assert:  
  
(x >= 1) || (tools::max_value<T>() * x >= *p_derivative)  
  
is true in my case, because in "tools::max_value<T>() * x >= *p_derivative" the left side is 0 and the right side is zero, too.  
  
So the division by 0:      *p_derivative /= x;  
  
gives NaN for the derivate. Is that okay? Or introduce another if?

---

## Comment 11

> Username: cohomology  
> Created_at: 2025-04-28 12:42:21 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2835119576  

Ok, adjusted to give the result max/2 as before.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2025-04-28 15:04:15 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2835560355  

>I don't understand your changes in gamma.hpp.  
  
You're correct: I know I've only just merged these but they're old changes from about a year ago I was half way through and it looks like I missed a case.  
  
It *can* overflow, but only for x denormal or zero.  
  
Test case is for a=0.01, x = denom_min.  
  
Feel free to reinstate the old commented out code, and I'll try and figure out a way to get coverage later...

---

## Comment 13

> Username: cohomology  
> Created_at: 2025-04-29 07:59:38 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2837858396  

Ok, done. CI must still run through, but already was okay with yesterdays commit, except the sycl errors.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2025-05-18 15:18:14 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2889048121  

Thanks @cohomology .  
  
I was about to merge this and then realized that the new test is generating UBSAN failures in cpp_int's internal logic.  It would seem to be unrelated to this patch, but I'm wary of introducing a new "known failure".  
  
I've tried and failed to reproduce the issues locally, anyone else? @ckormanyos @mborland ?  
  
For the record the failure is:  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/git_issue_1249.test/clang-linux-18/debug/x86_64/link-static/threading-multi/visibility-hidden/git_issue_1249.run  
  
====== BEGIN OUTPUT ======  
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48: runtime error: left shift of 16962843832447060 by 50 places cannot be represented in type 'unsigned long long'  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:446:48: runtime error: left shift of 11191096789291741588 by 50 places cannot be represented in type 'unsigned long long'  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:446:48   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35: runtime error: left shift of 7029918509865828866 by 63 places cannot be represented in type 'unsigned long long'  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35   
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/13/../../../../include/c++/13/bits/basic_string.h:490:51: runtime error: unsigned integer overflow: 3 - 9 cannot be represented in type 'size_type' (aka 'unsigned long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/bin/../lib/gcc/x86_64-linux-gnu/13/../../../../include/c++/13/bits/basic_string.h:490:51   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35: runtime error: left shift of 562949953421312 by 54 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35: runtime error: left shift of 11544872091648 by 60 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35   
  
Running 1 test case...  
  
git_issue_1249.cpp(93): error: in "test_main": check (*__errno_location ()) == saveErrno has failed  
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35: runtime error: left shift of 14355223812243456 by 60 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48: runtime error: left shift of 900719066480640 by 20 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35: runtime error: left shift of 33030144 by 61 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48: runtime error: left shift of 17870283321406128128 by 27 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35: runtime error: left shift of 18446744073705357312 by 18 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:596:35   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:446:48: runtime error: left shift of 9223372036854775807 by 41 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:446:48   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48: runtime error: left shift of 14379386343318 by 60 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48   
  
git_issue_1249.cpp(93): error: in "test_main": check (*__errno_location ()) == saveErrno has failed  
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:446:48: runtime error: left shift of 2953679248783 by 42 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:446:48   
  
../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48: runtime error: left shift of 10335793219406891510 by 13 places cannot be represented in type 'limb_type' (aka 'unsigned long long')  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:441:48   
  
git_issue_1249.cpp(93): error: in "test_main": check (*__errno_location ()) == saveErrno has failed  
  
  
*** 3 failures are detected in the test module "Master Test Suite"  
```

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2025-05-18 15:26:13 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2889052532  

Update, definitely don't understand this one, we have:  
  
```  
limb_type shift  = static_cast<limb_type>(s % Int::limb_bits);  
```  
  
And this could (should actually) be marked as `const`, so we must have `0 <= shift < limb_bits`.  Only thing I can think of is a memory overrun bug, but all the ASAN tests are green and we've checked this stuff pretty carefully...?  Then again USAN is always right, given that it's a runtime check!

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2025-05-18 17:18:30 UTC  
> Updated_at: 2025-05-18 17:19:40 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2889106352  

I made some recent changes to `bitwise.hpp` in e1cc7ba66d72a444458cdce5d3335fec9518fd5c  
  
I'm not sure if this is real or bogus, but I do see that I made a cast to `unsigned long long` there, which might have been better as `double_limb_type`.  
  
Also I notice that these left shifts are exceeding the range of 64-bit. So maybe I messed that one up and these tests reveal that mess-up.  
  
On the other hand, this might be a new phenomenon.  
  
I can investigate this tomorrow in peace. But I'm not sure if this is the right place (in my changes) or if we have discovered a new phenomenon?  
  
Cc: @jzmaddock and @mborland and @cohomology

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2025-05-19 11:33:35 UTC  
> Updated_at: 2025-05-19 11:33:52 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2890674554  

Hi @jzmaddock in a [PR in multiprecision](https://github.com/boostorg/multiprecision/pull/689), I have reverted the recent changes in `cpp_int/bitwise.hpp` that I had made.  
  
As soon as that cycles green (as it is expected to), I will merge it into develop of Multiprecision.  
  
Could we then, at that point in time, repeat these UBSAN tests? And then we might be able to find out if the recent changes in `cpp_int/bitwise.hpp` are related to the UBSAN detections in this Math PR.  
  
Cc: @mborland and @cohomology

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2025-05-19 11:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2890720198  

Not so easy to re-run the drone tests... ah, maybe closing and re-opening might do it?  Also need to wait half a day after multiprecision has been merged for the master project to catch up.  
  
Might be easier... to import the failing test into multiprecision on your own fork/branch and test locally that way?  That's what I was thinking of, but have run out of time at least for today.  That would need replicating the failing runner on Gibhub actions as well.  But it might make it easier to rapidly get to the cause (there's only really one test runner that needs running) without changing develop?

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2025-05-19 11:59:15 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2890745475  

> Might be easier... to import the failing test into multiprecision on your own fork/branch and test locally that way?  
  
Something like that. I could even spin up a dedicated repo and a simplified CI for this purpose.  
- Are the sanitizer detections restricted to the file `git_issue_1249.cpp` only?

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2025-05-19 12:00:50 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2890749790  

Hmmmm... Thinking out loud. Why don't I see if I can reproduce the UBSAN issues locally?  
  
I will report back.

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2025-05-19 14:58:52 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2891352451  

> Something like that. I could even spin up a dedicated repo and a simplified CI for this purpose.  
  
Yes exactly  
  
> Are the sanitizer detections restricted to the file git_issue_1249.cpp only?  
  
Yes.  
  
> Thinking out loud. Why don't I see if I can reproduce the UBSAN issues locally?  
  
Even better if you can, I don't have a suitable Linux machine setup at present.  
  
I did add a multitude of asserts for all those functions and couldn't detect anything with msvc or mingw though.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2025-05-22 18:05:22 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2902114604  

Sorry for the noise, apparently closing and re-opening doesn't trigger a drone re-build :(

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2025-05-22 19:12:10 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2902290285  

I think the one failure is a false positive, and if everyone agrees I'll disable that test under `-fsanitize=integer`.  
  
I made a cut down test PR here: https://github.com/boostorg/math/pull/1267 which pulls multiprecision from the integration_check_do_not_merge branch.  
  
I added asserts to the effected code like this:  
  
```  
      BOOST_ASSERT(shift < sizeof(pr[0]) * CHAR_BIT);  
      pr[rs - 1 - i] = pr[rs - 1 - i - offset] << shift;  // sanitizer still fails here  
```  
  
```  
../../../boost/multiprecision/cpp_int/bitwise.hpp:443:48: runtime error: left shift of 16962843832447060 by 50 places cannot be represented in type 'unsigned long long'  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/multiprecision/cpp_int/bitwise.hpp:443:48   
```  
  
And I think we were misreading this: it's complaining that the value stored in `pr[rs - 1 - i - offset]` can not be left shifted without loss of digits... well yes, that's what we want!!  This is not UB either, despite what the message says.  
  
It would be possible to suppress the error by masking off the operand to keep just the bits we're keeping post-shift, but that's silly and hamstrings the code unnecessarily.  
  
So basically, my conclusion, is we should just not running the unsigned-integer sanitizer on this code, as it's much too picky and we already have a USAN run anyway.

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2025-05-22 19:22:34 UTC  
> Updated_at: 2025-05-22 19:31:16 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2902312303  

Hi John @jzmaddock I've been busy in another office. And I could not actually do anything locally.  
  
That all makes sense. I had also wanted to add that I have been running `cpp_int` signed and unsigned in fuzzing runs with random keys in nightly builds in another project for well over a year now and never encountered a wrong numerical answer. I run add, sub, mul, div, sqrt and prime for 15 minutes each in those non-boost nightly runs. So I've got high confidence in `cpp-int`. This is versus my own int. So there have been a lot of calculations going down.  
  
Cc: @mborland

---

## Comment 25

> Username: mborland  
> Created_at: 2025-05-22 19:28:23 UTC  
> Url: https://github.com/boostorg/math/pull/1251#issuecomment-2902341316  

> So basically, my conclusion, is we should just not running the unsigned-integer sanitizer on this code, as it's much too picky and we already have a USAN run anyway.  
  
I would agree with this since shifting bits off the left side of an unsigned integer is well defined (they are discarded).

---
