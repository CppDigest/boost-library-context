# #327 Improve CI testing of tgamma at various precisions. [Merged]

> Username: jzmaddock  
> Created at: 2021-05-21 20:27:43 UTC  
> Updated at: 2021-06-03 15:30:01 UTC  
> Merged at: 2021-05-26 08:11:36 UTC  
> Closed at: 2021-05-26 08:11:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327  

Also run the special function tests for select compilers.  
NOTE: this still has some failures which need investigating.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-05-23 08:42:58 UTC  
> Updated_at: 2021-05-23 08:44:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-846527146  

Hi @jzmaddock what I do see in test failures are sporadic over-steppings of tolerance(s) for bin float at 80 or 100 decimal digits.  
  
These are probably beyond or at the very top limit of Lanczos approximation. Recalling that dec float (which seemingly passes all such cases) has quite a few more guard digits and also recalling that around that 100 digit region, we are either in the upper reaches of Lanczos or have already switched over to asymptotic (lossy) Stirling's approximation, I wonder if simply the tolerance needs to be raised for bin float?  
  
I was unable to extract an exact test case for microscopic inspection in a standalone test case on my work station. Do you think it would be possible to isolate an exact failing test case regarding the exact arguments and tolerance being used?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-05-23 09:01:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-846529241  

Nod.  Investigating now.  
  
The code that generated the approximations had an upper limit on the test data used to train the approximations, so they're really good within the test range of the generator, and then fade.  That's fine for double etc due the limited range of those types, but once you have int-sized exponents, things get more extreme.  I have a better approximation generated at 80-bit, but it fades for lgamma but not tgamma for reasons I don't understand yet.  And yes, switching to the asymptotic approximation is probably the ultimate fix, albeit I need to write some more tests first.  I'd like to try and understand things a bit more first, before I just stick an asymptotic sticking plaster over things too ;)

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-05-23 09:07:26 UTC  
> Updated_at: 2021-05-23 09:07:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-846529868  

>  I'd like to try and understand things a bit more first, before I just stick an asymptotic sticking plaster over things too  
  
Wow Indeed. Thank you John. That is really strong --- and the better way to go if possible, as those Lanczos approximations are super efficient.  
  
If I can help, please contact me. I also have a program that generates Lanczos coefficients, but for some reason I always lose something like half the precision of the type when I use the coefficients that are generated from my program. I have never really found the right mojo for the _N_ and _g_ parameters.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-05-23 10:38:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-846541217  

> I also have a program that generates Lanczos coefficients, but for some reason I always lose something like half the precision of the type when I use the coefficients that are generated from my program. I have never really found the right mojo for the N and g parameters.  
  
Nod.  Lanczos is notorious for being ill-conditioned.   However, there are reasonably well known "sweet spots" for N and g (and you can find more for larger N) which you can scan through for whichever one gives the best-conditioned result at fixed precision.  That's what this program does: https://github.com/boostorg/math/blob/develop/tools/lanczos_generator.cpp.  Note that while it prints out the "best" approximation at the end, it's better to use some subjective judgement as there may be one with nearly the same error, but many fewer terms.  
  
And finally.... the issue I had with lgamma and my revised 80-digit lanczos approximation was this: we use a rewrite of the Lanczos approximation in terms of z-1 or z-2 near 1 and 2 respectively, with differently generated coefficients for those special cases.  It turns out - and this should have been obvious with hindsight - that the best general approximation, isn't necessarily the best for the rearranged form near 1 and 2.  So I need to adjust the generator program to print the error rates in those special cases, and then I can hopefully just cut and paste the best bits.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-05-23 12:34:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-846556147  

>  issue I had with lgamma and my revised 80-digit lanczos approximation was this: we use a rewrite of the Lanczos approximation in terms of z-1 or z-2 near 1 and 2 respectively, with differently generated coefficients for those special cases. It turns out - and this should have been obvious with hindsight - that the best general approximation, isn't necessarily the best for the rearranged form near 1 and 2. So I need to adjust the generator program to print the error rates in those special cases, and then I can hopefully just cut and paste the best bits.  
  
What an awesome analysis! Right on.  
  
I don't think this really makes sense for 80, 100, or more decimal digits, but sometimes for smaller digit ranges on timy platforms (as you know I'm often hammering with math stuff) at those 1, 2 points on the real axis, I look into stuff like `N[Series[1/Gamma[x], {x, 1, 12}], 21]`. It's cool for float, but not so cool for 80 digits.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-05-25 11:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-847773971  

Hi @jzmaddock Sorry if I misunderstood. Some test failures seem to be in `cpp_dec_float-hpp`, which still (and maybe this was a misunderstanding) has `#include <boost/multiprecision/detail/big_lanczos.hpp>`.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-05-25 11:13:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-847779780  

> Sorry if I misunderstood. Some test failures seem to be in cpp_dec_float-hpp, which still (and maybe this was a misunderstanding) has #include <boost/multiprecision/detail/big_lanczos.hpp>.  
  
Oh :(   It was passing locally, but changes to develop was putting the #include back in - fixed now.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-05-26 08:11:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-848563651  

The one failure looks spurious.... merging.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-05-26 09:44:38 UTC  
> Updated_at: 2021-05-26 09:45:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-848628955  

This is great, John. Thank you. I really appreciated your program to calculate Lanczos coefficients and learned a lot as well.  
  
It's always amazing what improvements we find in the wily... `tgamma` over a wide range of precision and argument.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-05-26 12:09:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-848715585  

>It's always amazing what improvements we find in the wily... tgamma over a wide range of precision and argument.  
  
Haha, I sometimes think that each special function is a lifetimes study in it's own right!

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-05-26 12:39:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-848735073  

Indeed. These improvements also fit well with potential forward motion on double-double, quad-double and the like, as these are in the Lanczos precision range.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-06-03 15:30:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/327#issuecomment-853958929  

This PR has fixed #325

---
