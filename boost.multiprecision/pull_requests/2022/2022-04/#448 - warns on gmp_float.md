# #448 warns on gmp_float [Merged]

> Username: ckormanyos  
> Created at: 2022-04-24 12:15:55 UTC  
> Updated at: 2022-04-26 04:56:57 UTC  
> Merged at: 2022-04-26 04:56:56 UTC  
> Closed at: 2022-04-26 04:56:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/448  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-04-25 05:34:20 UTC  
> Updated_at: 2022-04-25 05:36:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/448#issuecomment-1108096667  

OK for review purposes, here is my report on enhanced warning treatment in `gmp.hpp`.  
  
I used the warning set:  
  
```sh  
-Wall -Wextra -Wconversion -Wsign-conversion -Wshadow -Wundef -Wunused-parameter -Wuninitialized -Wunreachable-code -Winit-self -Wzero-as-null-pointer-constant  
```  
  
Whith these:  
  
1. About 150 warnings were found and edited.  
2. Two remain needing review.  
3. Missing headers `<cstring>`, `<iomanip>` (mentioned in #450) and probably `<iostream>` and maybe `<algorithm> ` (needed for `std::abs()`). Others come in parasitically.  
  
Let's review point 2:  
The two remaining warnings (at this point in the game) can be seen [here](https://github.com/ckormanyos/boost_multiprecision_harden/runs/6152143551?check_suite_focus=true).  
  
On the first left-open warning at [this line](https://github.com/boostorg/multiprecision/blob/47725952b75bec3bbef944402a6d72ec9ca2e0f6/include/boost/multiprecision/gmp.hpp#L2147), I would recommend using the `min` value of the negative type if available. Otherwise explicitly casting and negating the value? It's not a bug since the bit pattern is 0x80-zeros-ish, but I'd like to _see_ the sign. Thoughts?  
  
On the second left-open warning at [this line](https://github.com/boostorg/multiprecision/blob/47725952b75bec3bbef944402a6d72ec9ca2e0f6/include/boost/multiprecision/gmp.hpp#L3009), we find that the actual subroutine's third parameter is unsigned. The code as it stands does implicit cast of `long` to GMP's `uint`. I was wondering if this needs explicit sign treatment for large, negative values. Or if that thirs parameter should simply be explicitly casted? This one has the code smell of potential trouble also.  
  
John (@jzmaddock) if you get a chance, please review and advise, also on anything else you find...  
  
Along the way, we need to figure out how to handle #450, which i'd be happy to merge and deal with in one swoop as we move forward...  
  
Cc: @mborland and @NAThompson

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-04-25 08:16:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/448#issuecomment-1108231669  

>On the first left-open warning at [this line](https://github.com/boostorg/multiprecision/blob/47725952b75bec3bbef944402a6d72ec9ca2e0f6/include/boost/multiprecision/gmp.hpp#L2147), I would recommend using the min value of the negative type if available. Otherwise explicitly casting and negating the value? It's not a bug since the bit pattern is 0x80-zeros-ish, but I'd like to see the sign. Thoughts?  
  
We can't use numeric_limits if that's what you mean - it's not (always) specialized for that type, so I would just cast the unsigned type to signed after we've done the bit-fiddling.  
  
>On the second left-open warning at [this line](https://github.com/boostorg/multiprecision/blob/47725952b75bec3bbef944402a6d72ec9ca2e0f6/include/boost/multiprecision/gmp.hpp#L3009), we find that the actual subroutine's third parameter is unsigned. The code as it stands does implicit cast of long to GMP's uint. I was wondering if this needs explicit sign treatment for large, negative values. Or if that thirs parameter should simply be explicitly casted? This one has the code smell of potential trouble also.  
  
OK in the interests of safety: if the second param (denominator) is negative, we make it positive and negate the first param (numerator), then add the cast.  
  
>Along the way, we need to figure out how to handle https://github.com/boostorg/multiprecision/pull/450, which i'd be happy to merge and deal with in one swoop as we move forward...  
  
Nod.  Probably the easiest way?  
  
Otherwise this all looks good, I wonder are you able to test with msvc and MPIR at -W4, as MPIR is subtly different to GMP?  Different warnings from msvc as well.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-04-25 10:59:47 UTC  
> Updated_at: 2022-04-25 11:00:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/448#issuecomment-1108418294  

> We can't use numeric_limits if that's what you mean - it's not (always) specialized for that type, so I would just cast the unsigned type to signed after we've done the bit-fiddling.  
  
OK. I came up with [this line](https://github.com/ckormanyos/boost_multiprecision_harden/blob/a171b124971deab8ab34c999bcee7f56ab86b0ee/boost/multiprecision/gmp.hpp#L2147)  
  
I will also kick out the `constexpr const` while we are on such a roll... Within the subroutine body, simply `constexpr` ought to do the trick. I'll also search for `constexpr const` and `const constexpr` as some global serach-replaces seem to have been done around those sequences.  
  
> OK in the interests of safety: if the second param (denominator) is negative, we make it positive and negate the first param (numerator), then add the cast.  
  
Affirmative. Will do.  
  
> Nod. Probably the easiest way?  
  
OK. I'll go ahead and `#include` also `<algorithm>`, `<cstring>`, `<iostream>`, and `<iomanip>`.  
  
> wonder are you able to test with msvc and MPIR at -W4, as MPIR is subtly different to GMP.  
  
Yes. I have an built MPIR on `win*` hanging around somewhere. That is a great idea. I will take a second pass on what I've already done in both `cpp_bin_float` as well as `gmp_float` on MSVC level 4 warnings. I've only ogt a 2019 running on my main workstation. 2022 I can check out after that.  
  
So... I will finish the `gmp.hpp` warnings, handle the review comments as agreed here, patch as agreed in place of #450. That'll take an afternoon or so to do and cycle through CI.  
  
After that, I'll merge to develop.  
  
After that I'll move on to either `cpp_dec_float` or MPFR's backend. This is a great experience that teaches me a lot.  
  
John, one last question, (@jzmaddock) I am a bit weak on rational. So if/whenever we get around to rational, I could use some rudimentary test code. I recall we made some tests while hardening rational a while back. Something like with a Zeta function. Did you ever formulate this into an official example?  
  
Cc: @mborland and @NAThompson

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-04-25 11:06:04 UTC  
> Updated_at: 2022-04-25 11:07:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/448#issuecomment-1108425173  

>  if the second param (denominator) is negative, ...  
  
Uggghhh John (@jzmaddock) should we catch zero denominator? Or just let it through to the compiled GMP code? This would be more than already done in the `unsigned long` versoin, so I guess not? Thoughts?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2022-04-25 19:01:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/448#issuecomment-1108929137  

I see just one warning from msvc:  
  
```  
2>d:\data\boost\boost\boost\multiprecision\gmp.hpp(2914,20): warning C4244: 'initializing': conversion from 'mpir_ui' to 'unsigned long', possible loss of data  
```  
  
A cast is safe and appropriate here, since *our* argument to the gcd is also an `unsigned int` so the result will always fit in that type (MPIR changes the argument to mpz_gcd_ui to something wider than `unsigned`, hence the warning).  
  
>John, one last question, (@jzmaddock) I am a bit weak on rational. So if/whenever we get around to rational, I could use some rudimentary test code. I recall we made some tests while hardening rational a while back. Something like with a Zeta function. Did you ever formulate this into an official example?  
  
The test_arithmetic_*.cpp tests are the best ones here as they basically "instantiate everything".  
  
>Uggghhh John (@jzmaddock) should we catch zero denominator? Or just let it through to the compiled GMP code? This would be more than already done in the unsigned long versoin, so I guess not? Thoughts?  
  
Ugh, yes, and the 2-arg constructor `mpq_rational(2, 0)` fails as well :(  These should all be throwing an `overflow_error` the same as `rational_adaptor` does IMO.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2022-04-25 20:03:01 UTC  
> Updated_at: 2022-04-25 20:03:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/448#issuecomment-1108984602  

> A cast is safe and appropriate here  
  
Got it. Yes I also saw that one on MSVC level 4 warning. I will catch it in the next push.  
  
> Ugh, yes, and the 2-arg constructor `mpq_rational(2, 0)` fails as well :( These should all be throwing an `overflow_error` the same as rational_adaptor does IMO.  
  
No problem. I can integrate the `throw`s in the next push.  
  
I will take a quick look around for any `mpf_`-divide-like stuff and see if I observe any other potential trouble.  
  
I do, however, have a good mind to push the pending changes in this PR to develop prior to making any functional (or error-throwing) changes. This kind of makes sense to me since this PR is already about 200 edits deep into changes. Might be good to finish it off and hit a new PR for these final refinements on GMP.  
  
Thank you John!

---
