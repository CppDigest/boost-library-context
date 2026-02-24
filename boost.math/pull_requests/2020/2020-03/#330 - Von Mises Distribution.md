# #330 Von Mises Distribution [Closed]

> Username: ghost  
> Created at: 2020-03-27 14:22:46 UTC  
> Updated at: 2024-02-22 10:27:45 UTC  
> Closed at: 2024-02-22 10:27:45 UTC  
> Url: https://github.com/boostorg/math/pull/330  

My trial to implement a circular distribution #296. Basic moments, PDF and CDF work for small values of κ, i. e. the concentration parameter. For larger values the used methods are too imprecise.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-04-05 12:44:05 UTC  
> Updated_at: 2020-04-05 16:50:40 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-609410736  

@pcjmuenster : My apologies for not being able to look at this; you will notice my comments were not super meaningful. We've got some work to do before the release; will probably not be able to look at this until after.  
  
Could you write a google benchmark for each, run it under AddressSanitizer and UBSan, and then do a couple plots?  
  
Also, I note your unit tests are mainly spot checks. Are there symmetries or integrals that could provide other tests?  
  
Also, note the file `test/math_unit_test.hpp`; it does a number of things which are specific to mathematics and are not found in `Boost.Test`. It also compiles much faster.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2020-04-05 12:55:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/330#pullrequestreview-387815169  

📁 include/boost/math/distributions/von_mises.hpp

```diff
   7 |+ 
   8 |+ #ifndef BOOST_STATS_VON_MISES_HPP
   9 |+ #define BOOST_STATS_VON_MISES_HPP
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:45:27 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403697072  

I think you've picked up an artefact of an old directory structure from other files. I'd change this to `BOOST_MATH_DISTRIBUTIONS_VON_MISES_HPP`.

> Username: pabristow  
> Created_at: 2020-04-05 16:22:00 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403723926  

Agree.

---

📁 include/boost/math/distributions/von_mises.hpp

```diff
   1 |+ //  Copyright John Maddock 2006, 2007.
   2 |+ //  Copyright Paul A. Bristow 2006, 2007.
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:45:41 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403697103  

Your name should be on the copyright.

> Username: pabristow  
> Created_at: 2020-04-05 16:22:10 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403723932  

definitely

---

📁 include/boost/math/distributions/von_mises.hpp

```diff
  13 |+ // http://mathworld.wolfram.com/VonMisesDistribution.html
  14 |+ 
  15 |+ #include <boost/math/distributions/fwd.hpp>
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:46:45 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403697210  

Can we remove the forward header? @jzmaddock  or @pabristow might have a better idea that I about why this is necessary but in general I find it causes problems.

---

📁 include/boost/math/distributions/von_mises.hpp

```diff
  73 |+ #ifdef BOOST_MSVC
  74 |+ #pragma warning(push)
  75 |+ #pragma warning(disable:4127)
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:47:52 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403697337  

Could you add a comment on why this warning should be disabled?

> Username: pabristow  
> Created_at: 2020-04-05 16:26:48 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403724699  

MSVC used to warn on anything that was constant.  
  
// C 4127 "conditional expression is constant"  
  
They have recently stopped doing this, at last, thank goodness!  
  
https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-4-c4127?view=vs-2019  
  
But we still test antediluvian versions of MSVC, so still worth keeping to reduce clutter?

---

📁 include/boost/math/distributions/von_mises.hpp

```diff
 122 |+     // Polynomial coefficients from boost/math/special_functions/detail/bessel_i0.hpp
 123 |+     // > Max error in interpolated form: 5.195e-08
 124 |+     // > Max Error found at float precision = Poly: 8.502534e-08
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:50:23 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403697656  

You are using `float` precision to do the interpolation, but allow result to be an arbitrary precision. Is there a better way?

> Username: Unknown  
> Created_at: 2020-04-07 10:55:38 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r404718701  

I don't understand what you are referring to. `pdf_impl` is overloaded depending on the precision.

---

📁 include/boost/math/distributions/von_mises.hpp

```diff
 113 |+ 
 114 |+   BOOST_MATH_STD_USING
 115 |+   if(conc < 7.75)
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:50:49 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403697703  

I guess `conc < 7.75` is a cutoff of an asymptotic expansion? Could you add a comment if so?

---

📁 include/boost/math/distributions/von_mises.hpp

```diff
 361 |+     RealType c = 24.0 * conc;
 362 |+     RealType v = c - 56;
 363 |+     RealType r = sqrt((54.0 / (347.0 / v + 26.0 - c) - 6.0 + c) / 12.0);
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:52:50 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403697950  

Lots of double precision constants here. Is arbitrary precision not possible?

> Username: Unknown  
> Created_at: 2020-04-07 06:44:43 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r404571335  

As for the CDF, the mentioned paper gives constants depending on the precision for the values of `ck`, `a1`–`a4` and `v`. However, they are only for upto 12 decimal digits. I tried to extrapolate these values but that wasn't quite successful. I have looked around for implementations in other libraries and they all seem to use this old paper and the values for 8 digits (single precision).

> Username: pabristow  
> Created_at: 2020-04-07 10:30:26 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r404705153  

That's a little disappointing (though probably good enough for most users).  
  
https://www.rdocumentation.org/packages/circular/versions/0.4-93 R documents mention this but I haven't found/downloaded the source code yet, but perhaps you already have?  
  
These fitting functions are a Black Art but I know the Chief Wizard and perhaps he can advise @jzmaddock .

> Username: jzmaddock  
> Created_at: 2020-04-07 16:15:22 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r404936717  

I do wonder if we can do better here:  
  
1) I notice you've pulled out and duplicated the contents of I0 - why not just call that function directly?  
2) Looking at the original paper, the series to evaluate for the CDF is given on the start of page 280 and everything else is just "faff" to try to evaluate that.  I confess it's going to be a nasty one to code up because the Bessel function recurrences are stable in the backwards not forwards direction.  But it would be true arbitrary precision.  
  
I faced something similar with some of the 1F1 series expansions, and the way I coded it was to maintain a cache of Bessel function values, when filling the cache you start with an arbitrary small value, then iterate to fill the cache and then normalise all the values afterwards either using the last value from the previous cache or a single Bessel function call.  Which is not easy to explain and may or may not make sense!    
  
The code in question is `hypergeometric_1F1_AS_13_3_7_tricomi_series::refill_cache` in https://github.com/boostorg/math/blob/8eb40afd6b2cdc3ffa786d4494bfc4164794e8d4/include/boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp#L153.  Note that this is way over-complex for your case because it deals with both Bessel I and J, and positive and negative orders, neither of which are needed here.

> Username: Unknown  
> Created_at: 2020-04-08 06:51:33 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r405295542  

The reason to duplicate the code was that I0(kappa) grows very fast just like e^kappa, dividing these two however leads to numbers < 100. So, in order to make the code work for large values of kappa, I calculate the quotient directly via polynomial approximation.

> Username: NAThompson  
> Created_at: 2020-04-17 13:35:17 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r410226225  

> The reason to duplicate the code was that I0(kappa) grows very fast just like e^kappa, dividing these two however leads to numbers < 100. So, in order to make the code work for large values of kappa, I calculate the quotient directly via polynomial approximation.  
  
Can you just test for values of kappa that cause overflow and then use an asymptotic there?


📁 test/test_von_mises.cpp

```diff
   1 |+ // Copyright Paul A. Bristow 2010.
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:53:53 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403698075  

Again, your name should be here.

---

📁 test/test_von_mises.cpp

```diff
  13 |+ // http://mathworld.wolfram.com/VonMisesDistribution.html
  14 |+ 
  15 |+ #include <pch.hpp> // include directory /libs/math/src/tr1/ is needed.
```

> Username: NAThompson  
> Created_at: 2020-04-05 12:54:13 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r403698122  

Do you need the entire `pch.hpp`? If not I'd just include what you need.

> Username: Unknown  
> Created_at: 2020-04-08 07:49:35 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r405324754  

Not necessary at all.

> Username: Unknown  
> Created_at: 2020-05-18 08:14:05 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r426444729  

Appearently necessary for some architectures …


---

## Comment 3

> Username: ghost  
> Created_at: 2020-04-07 09:21:37 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-610277028  

> Could you write a google benchmark for each, run it under AddressSanitizer and UBSan, and then do a couple plots?  
  
What kind of benchmarks do you have in mind?

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-04-07 09:43:20 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-610287284  

@pcjmuenster : [Here's](https://github.com/boostorg/multiprecision/pull/203) a google benchmark for the GCD in multiprecision. I'd do a float and double test for the pdf and cdf.

---

## Comment 5

> Username: ghost  
> Created_at: 2020-04-17 13:22:28 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-615241227  

>   
>   
> I'd do a float and double test for the pdf and cdf.  
  
Until now, I manually typed in the formula into Wolfram|Alpha to get a correct value. In order to make a plot, I'd have to retrieve hundreds or thousands of these. WA has an API to automate these kind of things but unfortunately caching the results is against the ToS. Do you another way to get the exact values? (The papers I read are so old, they only have 4 digits of precision)

---

## Comment 6

> Username: NAThompson  
> Created_at: 2020-04-17 13:32:53 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-615246358  

> Until now, I manually typed in the formula into Wolfram|Alpha to get a correct value. In order to make a plot, I'd have to retrieve hundreds or thousands of these.   
  
No don't do that.  
  
I'd do the following:  
  
- Test the evenness of the PDF f(-x) = f(x) for various kappa.  
  
- Test the oddness of the CDF about y = 0.5 for various kappa.  
  
- Test quadratures of the PDF and see that it agrees with the CDF. I think `tanh_sinh` should work well here, though over the entire domain use `trapezoidal`.  
  
- Test moments of the distribution; it looks like they can be evaluated as quadratures and then tested via modified Bessel functions.

---

## Comment 7

> Username: ghost  
> Created_at: 2020-04-29 07:22:00 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-621032909  

>   
>   
> > Until now, I manually typed in the formula into Wolfram|Alpha to get a correct value. In order to make a plot, I'd have to retrieve hundreds or thousands of these.  
>   
> No don't do that.  
>   
> I'd do the following:  
>   
>     * Test the evenness of the PDF f(-x) = f(x) for various kappa.  
>   
>     * Test the oddness of the CDF about y = 0.5 for various kappa.  
>   
>     * Test quadratures of the PDF and see that it agrees with the CDF. I think `tanh_sinh` should work well here, though over the entire domain use `trapezoidal`.  
>   
>     * Test moments of the distribution; it looks like they can be evaluated as quadratures and then tested via modified Bessel functions.  
  
So shall I plot the difference between say pdf(mu - x) and pdf(mu + x) and not between the calculated value and some yet to be retrieved exact value?

---

## Comment 8

> Username: pabristow  
> Created_at: 2020-04-29 08:26:15 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-621061792  

In the past we have used a modest number of spot values using Mathematica or some other 'known good' source.  Plot are pretty, but in the end they may not tell you much beyond the obvious over nearly all the range.    
  
But we have tried to include some spot values that are 'edge and corner' cases too.   These may reveal where a better algorithm would be useful (or has been used).

---

## Comment 9

> Username: NAThompson  
> Created_at: 2020-04-29 11:59:10 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-621156976  

> So shall I plot the difference between say pdf(mu - x) and pdf(mu + x) and not between the calculated value and some yet to be retrieved exact value?  
  
Don't do it as a plot, do:  
  
```  
#include "math_unit_test.hpp"  
  
template<typename Real>  
void test_even()  
{  
     Real mu = ...;  
     Real x = 0;  
     Real dx = ...;  
     while (mu + x < pi) {  
             CHECK_ULP_CLOSE(pdf(mu+x), pdf(mu-x), 4);  
              x+= dx;  
     }  
}  
```

---

## Comment 10

> Username: ghost  
> Created_at: 2020-05-06 06:31:43 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-624466922  

> ```  
> #include "math_unit_test.hpp"  
>   
> template<typename Real>  
> void test_even()  
> {  
>      Real mu = ...;  
>      Real x = 0;  
>      Real dx = ...;  
>      while (mu + x < pi) {  
>              CHECK_ULP_CLOSE(pdf(mu+x), pdf(mu-x), 4);  
>               x+= dx;  
>      }  
> }  
> ```  
  
Switching from BOOST_CHECK_CLOSE to CHECK_ULP_CLOSE revealed that the symmetry doesn't increase with the precision. Everything's fine for `float` but for `double` the ULPs off are about 1e+6.

---

## Comment 11

> Username: NAThompson  
> Created_at: 2020-05-06 11:34:09 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-624596783  

> Everything's fine for float but for double the ULPs off are about 1e+6.  
  
Yeah ULPs tests are pretty unforgiving! Try creating an [ulps plot](https://github.com/boostorg/math/blob/develop/include/boost/math/tools/ulps_plot.hpp) in float precision and you might be able to see where things are going wrong.

---

## Comment 12

> Username: pabristow  
> Created_at: 2020-05-06 13:22:55 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-624646190  

I'm currently playing with Nick's ulps toy (looks really helpful) and I'll put your distribution on my todo-next list.

---

## Comment 13

> Username: ghost  
> Created_at: 2020-05-13 16:11:07 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-628092608  

Appearantly the problem was the imprecision of the calculation of `mu - x` and `mu + x`. After switching to inputs that have a finite binary representation, PDF is now down to 2 ULPs and CDF to 32.

---

## Review 14 [Commented]

> Username: NAThompson  
> Created_at: 2020-05-13 16:19:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/330#pullrequestreview-411089976  

📁 test/test_von_mises.cpp

```diff
 338 | 
 339 |+ template <typename RealType>
 340 |+ void test_symmetry(RealType)
```

> Username: NAThompson  
> Created_at: 2020-05-13 16:19:11 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r424565866  

This doesn't need an argument does it?

> Username: Unknown  
> Created_at: 2020-05-13 17:30:03 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r424610360  

I just used the same style that also `test_spots` uses.


---

## Review 15 [Commented]

> Username: NAThompson  
> Created_at: 2020-05-13 16:22:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/330#pullrequestreview-411090912  

📁 test/test_von_mises.cpp

```diff
 347 |+       for (RealType x = 0; x < pi; x += delta) {
 348 |+         CHECK_ULP_CLOSE(pdf(dist, mean + x),
 349 |+                         pdf(dist, mean - x), 2);
```

> Username: NAThompson  
> Created_at: 2020-05-13 16:20:17 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r424566550  

Do you get 2 ULP accuracy in long double as well?

> Username: Unknown  
> Created_at: 2020-05-21 17:23:37 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r428799030  

Yes.

---

📁 test/test_von_mises.cpp

```diff
 349 |+                         pdf(dist, mean - x), 2);
 350 |+         CHECK_ULP_CLOSE(cdf(dist, mean + x) - static_cast<RealType>(0.5),
 351 |+                         static_cast<RealType>(0.5) - cdf(dist, mean - x), 32);
```

> Username: NAThompson  
> Created_at: 2020-05-13 16:20:39 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r424566818  

This is a great test.

---

📁 test/test_von_mises.cpp

```diff
 375 |+   test_symmetry(0.0);
 376 |+ 
 377 | #ifndef BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS
```

> Username: NAThompson  
> Created_at: 2020-05-13 16:21:27 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r424567326  

I think we can now assume long double support; this macro was for older systems.

---

📁 test/test_von_mises.cpp

```diff
 379 |+   //test_symmetry(0.0L);
 380 | #if !BOOST_WORKAROUND(__BORLANDC__, BOOST_TESTED_AT(0x0582))
 381 |   //test_spots(boost::math::concepts::real_concept(0.)); // Test real concept.
```

> Username: NAThompson  
> Created_at: 2020-05-13 16:21:53 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r424567582  

These lines can be deleted.

---

📁 test/test_von_mises.cpp

```diff
 389 | 
 366 |-    
 390 |+ 
```

> Username: NAThompson  
> Created_at: 2020-05-13 16:22:14 UTC  
> Updated_at: 2020-08-23 16:22:23 UTC  
> Url: https://github.com/boostorg/math/pull/330#discussion_r424567818  

Could you add a multiprecision test? Template it on float128 should be sufficient.


---

## Comment 16

> Username: NAThompson  
> Created_at: 2020-05-13 16:26:43 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-628101595  

Could you drop  
  
- an ulps plot of the pdf and cdf into `doc/graphs`?  
- a google benchmark into `reporting/performance`?

---

## Comment 17

> Username: pabristow  
> Created_at: 2020-05-14 10:51:16 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-628554484  

I think I remember that passing zero as in test_symmetry(0.0); is a left-over from the dark days only just in the current millennium when templates didn't work properly on all compilers.  
  
(But some of the testers may still using these old compilers/platforms, so we haven't been through to rationalise it.  I suspect the those that are marked as requiring C++11 may not try anyway?  
  
"If it ain't broke, don't fix it" applies?  )

---

## Comment 18

> Username: ghost  
> Created_at: 2020-06-21 19:37:49 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-647171998  

>   
>   
> Could you drop  
>   
>     * an ulps plot of the pdf and cdf into `doc/graphs`?  
>   
>     * a google benchmark into `reporting/performance`?  
  
I wasn't sure how to use the ULP plot function in Boost.Math to plot a function with two variables.  
So I made a color-coded one instead. What do you think? The red areas have an error of 260 ULPs.  
  
![ulp_plot_von_mises_pdf_float](https://user-images.githubusercontent.com/50599620/85233474-d2f07980-b406-11ea-86cd-8cb598fe74b5.PNG)

---

## Comment 19

> Username: NAThompson  
> Created_at: 2020-06-21 20:34:29 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-647178334  

@pcjmuenster : Looks pretty good. For our own ability to reproduce this later though, could you fix the concentration parameter to (say) 64 and then use the univariate ulps plots in `boost/math/tools`? Put the file you write in `reporting/accuracy/von_mises_ulps.cpp`.

---

## Comment 20

> Username: ghost  
> Created_at: 2020-08-23 16:24:47 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-678794654  

The ULP plots for the PDF look okay-ish, but the ones for the CDF clearly indicate that the cancelling out of significant digits at the end due to the formula causes severe imprecisions.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2020-08-23 16:44:06 UTC  
> Url: https://github.com/boostorg/math/pull/330#issuecomment-678796764  

> The ULP plots for the PDF look okay-ish, but the ones for the CDF clearly indicate that the cancelling out of significant digits at the end due to the formula causes severe imprecisions.  
  
Can you post a 1D ULPs plot in the "bad region" you are finding? The condition number envelope should tell us if those errors are acceptable or not.

---
