# #1305 - ENH: more inverses for distribution CDFs [Open]

> Username: dschmitz89  
> Created at: 2025-08-21 06:37:54 UTC  
> Updated at: 2026-02-21 12:55:36 UTC  
> Url: https://github.com/boostorg/math/issues/1305  

Over at https://github.com/scipy/scipy/pull/23308 I implemented the inverse of the noncentral F distibution CDF with respect to the noncentrality parameter (the legacy cdflib library called this `ncfdtrinc`) using boost. It is a straight forward exercise for `bracket_and_solve_root` and works well.  
  
Now my question is if such inverses are of interest for boost.math itself? For the noncentral [chi squared distribution](https://www.boost.org/doc/libs/1_42_0/libs/math/doc/sf_and_dist/html/math_toolkit/dist/dist_ref/dists/nc_chi_squared_dist.html), these inverses are for example implemented. Such functions have statistical applications (see for example here in [statsmodels](https://www.statsmodels.org/dev/_modules/statsmodels/stats/oneway.html)).  
  
Tracker:  
  
- [x] `noncentral_f:find_noncentrality` #1345   
- [ ] `noncentral_f:find_v1`  
- [ ] `noncentral_f:find_v2`  
   
- [x] `noncentral_t:find_degrees_of_freedom` #1355   
- [x] `noncentral_t:find_noncentrality` #1355   
  
- [ ] `fisher_f:find_v1`  
- [ ] `fisher_f:find_v2`  
  
- [ ] `student_t:invert_cdf_with_respect_to_degrees_of_freedom` (`find_degrees_of_freedom` is already taken, so we need another name ;) )

---

## Comment 1

> Username: mborland  
> Created at: 2025-08-22 09:27:58 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3213712801  

We will certainly take it if you are offering. I can review your code here or there if need be, just let me know.

---

## Comment 2

> Username: dschmitz89  
> Created at: 2025-08-22 10:24:17 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3213875298  

I had a look at the noncentral chi squared code, that looks almost reasonable to deal with for a non veteran C++ developer. What is more intimidating is how to setup the environment and create test cases. If I understand correctly, you use boost.multiprecision for that purpose. Do you have detailed docs somewhere?  
  
I will try to get to that in future but for the time being, if you have time to help out with the review in scipy that would be highly appreciated.

---

## Comment 3

> Username: mborland  
> Created at: 2025-08-22 11:23:56 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3214030666  

The distributions are in general tested against `float`, `double`, and `long double` with spot values calculated by Mathematica (and others). There's also a type called `real_concept` which emulates a multi-precision type, but is in practice a wrapper around a `long double`. I don't think there's any real documentation on the testing harness for the distributions. In your case the only types of concern are `float` and `double` right?

---

## Comment 4

> Username: dschmitz89  
> Created at: 2025-08-23 20:15:20 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3217331309  

Yep, float and double are all we need in scipy. Your testing mechanism do not seem to be so different from scipy. There we mostly rely on spot checks using some high precision references as well.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-08-24 08:04:59 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3217925767  

Note that we already have test data, it's just that if you use it in reverse (to compute non-centrality) then there may be some ill-conditioned inputs lurking in there.  If you look at the existing tests for the inverses, you'll see me filtering those out.

---

## Comment 6

> Username: dschmitz89  
> Created at: 2025-09-04 07:00:15 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3252205528  

To be more specific, the list of distributions for which we would like to implement the inverses of the CDF with respect to its other parameters (quantiles already exist) are:  
  
- noncentral F  
- noncentral t  
- fisher_f (regular F distribution)  
- Student t  
  
 If you are interested in all those, we can start in the coming months.

---

## Comment 7

> Username: mborland  
> Created at: 2025-09-04 07:21:16 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3252271409  

That would be great. If you want to merge a simplified version into scipy before boost you can ping me to review.

---

## Comment 8

> Username: JacobHass8  
> Created at: 2026-01-29 17:33:39 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3819192919  

@dschmitz89 I'll start implementing the rest of these inverses in a new PR!

---

## Comment 9

> Username: dschmitz89  
> Created at: 2026-01-29 18:14:52 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3819381796  

> @dschmitz89 I'll start implementing the rest of these inverses in a new PR!   
  
Thanks, your help is highly appreciated 👍. I suggest to open one PR per function, that is typically more manageable for maintainers to review.

---

## Comment 10

> Username: ckormanyos  
> Created at: 2026-01-29 19:37:58 UTC  
> Updated at: 2026-01-29 19:39:51 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3819875252  

> Yep, float and double are all we need in scipy. Your testing mechanism do not seem to be so different from scipy. There we mostly rely on spot checks using some high precision references as well.  
  
We often consider Multiprecision types also. You know, I'm not sure if we have anything like a visible _requirement_ to mandate that new special functions adhere to multiprecision with scalable algorithms. Sometimes it just doesn't work anyway.  
  
But we have observed that many clients jump up to 113-binary digit float (i.e., `float128`). So if you can get $34$ decimal digits and test them as well, this can be a real plus down the road for some of our most demanding clients.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2026-01-29 19:48:27 UTC  
> Updated at: 2026-01-29 19:49:51 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3819924137  

> if you can get $34$ decimal digits   
  
This usually shakes out straight "off the rack" since some of our platforms have $128$-bit `long double`.  
  
But if you get that far, you can go for $50$ decimal digits too. That's usually enough for really demanding real-world uses, even if it's just a couple folks needing such precision.

---

## Comment 12

> Username: JacobHass8  
> Created at: 2026-01-30 00:45:38 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3821165031  

>       [ ]  `noncentral_t:find_degrees_of_freedom`  
>       [ ]   
>       [ ]  `noncentral_t:find_noncentrality`  
  
It appears that these are already implemented [here](https://github.com/boostorg/math/blob/bf511b46a69d9d0648fe21a415eb41bdd3e17f78/include/boost/math/distributions/non_central_t.hpp#L867C1-L954C7) but are disabled with the comment "This code is disabled, since there can be multiple answers to the question, and it's not clear how to find the "right" one." I'm not exactly sure what that means though. @jzmaddock it looks like you wrote most of this. Do you know what you meant by this?

---

## Comment 13

> Username: jzmaddock  
> Created at: 2026-01-30 10:32:18 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3823010631  

>It appears that these are already implemented [here](https://github.com/boostorg/math/blob/bf511b46a69d9d0648fe21a415eb41bdd3e17f78/include/boost/math/distributions/non_central_t.hpp#L867C1-L954C7) but are disabled with the comment "This code is disabled, since there can be multiple answers to the question, and it's not clear how to find the "right" one." I'm not exactly sure what that means though. @jzmaddock it looks like you wrote most of this. Do you know what you meant by this?  
  
It's too long ago!!  
  
Sorry I can't remember what the specifics of the problem were, but I must have hit something intractable.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2026-01-30 13:25:00 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3823738029  

I think I might have messed up here - by this point we'd just done a bunch of multi-modal distributions with no clear inverses, but in this case I think there's a bug in the implementation somewhere... will investigate!

---

## Comment 15

> Username: jzmaddock  
> Created at: 2026-02-01 13:18:06 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3831035007  

Re-enabled the non-central T code here: https://github.com/boostorg/math/pull/1355  
  
There were quite a few issues with regard to understanding whether the function being inverted was rising or falling, which makes me wonder if the the new non-central-f code may have some of the same issues lurking.  
  
Random cool discovery (to me anyway), was that at x=0 the CDF is completely independent of the number of degrees of freedom, and depends only on the non-centrality (so inverting the degrees of freedom is only really possible out in the tails).  
  
Likewise, very large degrees of freedom are not invertible with any accuracy as we get increasingly closer to a normal distribution (and the actual value of v becomes irrelevant).

---

## Comment 16

> Username: JacobHass8  
> Created at: 2026-02-01 21:26:01 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3832076466  

> There were quite a few issues with regard to understanding whether the function being inverted was rising or falling, which makes me wonder if the the new non-central-f code may have some of the same issues lurking.  
  
What do you mean by this? Is it the change you have here?   
  
```C++  
std::pair<RealType, RealType> ir = tools::bracket_and_solve_root(  
               f, guess, RealType(2), x < 0 ? false : true, tol, max_iter, pol);  
```

---

## Comment 17

> Username: jzmaddock  
> Created at: 2026-02-02 10:19:36 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3834224970  

> What do you mean by this? Is it the change you have here?  
  
Among others, but false alarm: the non-central T can have negative non-centrality as well as negative x, and it all does interesting things to the slope of the functions being inverted.  Non-central F is thankfully all positive!

---

## Comment 18

> Username: jzmaddock  
> Created at: 2026-02-21 12:55:36 UTC  
> Url: https://github.com/boostorg/math/issues/1305#issuecomment-3938739236  

Re non-central F and find v1/v2, what happens if there is more than one answer?  
  
For example with:  
  
```  
   double v1 = 5;  
   double v2 = 2;  
   double nc = 1;  
   double x = 1.5;  
   double P = 0.49845842011686358665786775091245664;  
```  
  
And searching for v1 we have:  
  
<img width="1000" height="600" alt="Image" src="https://github.com/user-attachments/assets/441220f7-79bd-4f47-a630-68fb93709ce6" />  
  
ie two answers at ~ 0.6 and 5.  
  
This just happens to be the first problem case I looked at, it's not clear that it doesn't get worse still ;)
