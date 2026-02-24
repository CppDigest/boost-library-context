# #196 Gauss laguerre [Merged]

> Username: ckormanyos  
> Created at: 2020-02-16 18:57:04 UTC  
> Updated at: 2020-02-27 18:20:40 UTC  
> Merged at: 2020-02-27 18:20:04 UTC  
> Closed at: 2020-02-27 18:20:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196  

Remove unused variables.  
Check with GCC's -Wall -Wextra -pedantic and a few more warnings.  
Remove all MSVC level 3 warnings.  
Other compiler warnings not checked.  
Eliminate unused logic paths.  
Speedup and calibrate for 50...1200 decimal digits.  
Use a few more Boost macros, but not yet fully constexpr.  
Repair the correct author and update the copyright information.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2020-02-16 19:05:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-586741019  

This PR is intended to implement the first round or repairs and improvements of the Gauss Laguerre quadrature example from 2012-2015.  
  
Some of these points have been mentioned in #193

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2020-02-17 06:17:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-586834945  

Actually, I would like to extend this example.  
Will close this PR without merging it.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2020-02-22 17:30:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-589979307  

The Gauss-Laguerre quadrature example has been reworked.  
The approximation now handles arguments on the real axis airy_ai(x) with x.gt.1.  
The example now checks a range of arguments with control values from `cyl_bessel_k` of 1/3.  
The idea of this example is now much more clear, as mentioned in code comments.  
  
- This example is intended to handle airy_ai(x) calculations for medium to large arguments  
- Use quadrature for x >=1  
- Assume that and a hypergeometric function or a Taylor series could be used for x < 1.  
  
I will probably merge my own PR in a day or so, assuming the tests run.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-02-22 17:44:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-589980622  

Very cool. Just a few comments: I added a progress bar into:  
  
https://github.com/boostorg/math/blob/develop/example/naive_monte_carlo_example.cpp  
  
It seems like this example might also benefit from the `display_progress` function, since it takes a while to run, with no output for some time, and then lots of output which could potentially be better expressed as a progress bar.  
  
I ran this example through the Sanitizer Suite over both clang++-9 and g++-7; it came out clean. So looks good to me!

---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2020-02-22 17:45:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/196#pullrequestreview-363041381  

📁 example/gauss_laguerre_quadrature.cpp

```diff
 244 |+         first_laguerre_root = (j_alpha_m1_sqr * (   -T(0.6666666666667F)
 245 |+                                                  + ((T(0.6666666666667F) * alpha) * alpha)
 246 |+                                                  +  (T(0.3333333333333F) * j_alpha_m1_sqr) + vf2)) / (vf2 * vf);
```

> Username: NAThompson  
> Created_at: 2020-02-22 17:45:19 UTC  
> Updated_at: 2020-02-26 19:29:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#discussion_r382929140  

Doesn't this lose precision? I might do T(2)/T(3), or boost::math::constants::two_thirds.


---

## Comment 6

> Username: pabristow  
> Created_at: 2020-02-22 17:52:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-589981414  

I pondered on this too, but concluded that it is only a guess, so double is more than good enough?  
  
Looks good (if it took a little while for a superannuated FORTRAN programmer to et his head around some of the lambda-ing).  While this is indubitably cool, is there a performance reason for doing it this way?  
  
From: Nick <notifications@github.com>  
Sent: 22 February 2020 17:45  
To: boostorg/multiprecision <multiprecision@noreply.github.com>  
Cc: Subscribed <subscribed@noreply.github.com>  
Subject: Re: [boostorg/multiprecision] Gauss laguerre (#196)  
  
  
@NAThompson commented on this pull request.  
  
________________________________  
  
In example/gauss_laguerre_quadrature.cpp<https://github.com/boostorg/multiprecision/pull/196#discussion_r382929140>:  
  
> -      }  
  
-      else  
  
-      {  
  
-        // For larger alpha, use the first line of Eqs. 10.21.40 in the NIST Handbook.  
  
-        const T alpha_pow_third(boost::math::cbrt(alpha));  
  
-        const T alpha_pow_minus_two_thirds(T(1) / (alpha_pow_third * alpha_pow_third));  
  
-  
  
-        j_alpha_m1 = alpha * (((((                             + 0.043F  
  
-                                  * alpha_pow_minus_two_thirds - 0.0908F)  
  
-                                  * alpha_pow_minus_two_thirds - 0.00397F)  
  
-                                  * alpha_pow_minus_two_thirds + 1.033150F)  
  
-                                  * alpha_pow_minus_two_thirds + 1.8557571F)  
  
-                                  * alpha_pow_minus_two_thirds + 1.0F);  
  
+        first_laguerre_root = (j_alpha_m1_sqr * (   -T(0.6666666666667F)  
  
+                                                 + ((T(0.6666666666667F) * alpha) * alpha)  
  
+                                                 +  (T(0.3333333333333F) * j_alpha_m1_sqr) + vf2)) / (vf2 * vf);  
  
Doesn't this lose precision? I might do T(2)/T(3), or boost::math::constants::two_thirds.  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/pull/196?email_source=notifications&email_token=AAIG4ALUEXBVWDD4XKKKTDTREFQDBA5CNFSM4KWFWCJ2YY3PNVWWK3TUL52HS4DFWFIHK3DMKJSXC5LFON2FEZLWNFSXPKTDN5WW2ZLOORPWSZGOCWRZEZI#pullrequestreview-363041381>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4AKHF6M5DRHDHFTJHMTREFQDBANCNFSM4KWFWCJQ>.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-02-22 17:56:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-589981719  

Whoops, didn't notice that it's a guess; my bad.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2020-02-23 11:25:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-590058627  

> It seems like this example might also benefit from the display_progress function, since it takes a while to run, with no output for some time, and then lots of output which could potentially be better expressed as a progress bar.  
  
Kewl. I will check it out...  
  
I think this example is progressing to be about where I would like it to be.  
  
Based on some new knowledge that I acquired in this area and extension of the example, I think it will make sense that I add some further sentences and an equation or two to the docs, as time permits.  
  
Thanks for the help, and kind regards, Chris

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2020-02-23 11:26:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-590058652  

Is there a simple reason anyone knows why the builds are failing on CI?  
  
I can't see how my changes caused these failures. But I am still investigating...

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2020-02-23 13:15:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-590067682  

>> It seems like this example might also benefit from the display_progress function, since it takes a while to run, with no output for some time, and then lots of output which could potentially be better expressed as a progress bar.  
  
> Kewl. I will check it out...  
  
Progress percent indications have been added.

---

## Comment 11

> Username: NAThompson  
> Created_at: 2020-02-23 13:24:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-590068483  

@ckormanyos : Just tried it out; looks great!

---

## Review 12 [Commented]

> Username: pabristow  
> Created_at: 2020-02-24 17:59:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/196#pullrequestreview-363587126  

Testing especially looking much better.  
  
Ran OK on Windows 10 VS community 16.5.0 Preview 3.0  Release 64bit  fp:precise and /sdl checks  
  
also ran using /fp:fast and no /sdl checks with identical results and timing  (2:50 )  
  
I noted this remaining comment  
  
// If we boostify this, one could throw a range error here.  
// If so, then also document it in the docs.  
  
this might mean it needs  
#include <exception>  ?  
  
Produces a lot of output - perhaps over filling a users buffer? but is OK.  
  
As far as I can see all the results are 'exact', so perhaps the guard digits are a little over-enthusiastic?  
  
Looks good to me.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2020-02-24 21:06:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-590548302  

> Testing especially looking much better.  
  
Yup. Thank you. It was a good idea from your side to clean up this example. I learned a lot and I think the example is much better now.  
  
> I noted this remaining comment...  
  
Which is now expressed differently, thereby removing the open endedness of the comment.  
  
More detailed comments describing the example are also now near the head of the file.  
  
> As far as I can see all the results are 'exact', so perhaps the guard digits are a little over-enthusiastic?  
  
I have removed the guard digits. It was necessary to slightly increase the tolerance of close fraction comparison with the control values. But I do, in fact, prefer this now without the guard digits. It's a bit cleaner to describe and comprehend.  
  
The tests are now cycling. I had a few problems with using some C++11 stuff on the appveyor runs. But I switched over to the Boost macros such as those for older compilers lacking some C++11 features.  Barring any unexpected failures, I will handle this PR myself after the tests cycle.  
  
Thanks and kind regards, Chris

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2020-02-27 18:20:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/196#issuecomment-592104435  

The Gauss-Laguerre quadrature example has been refactored and improved.

---
