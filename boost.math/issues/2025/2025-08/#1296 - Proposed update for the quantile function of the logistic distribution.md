# #1296 - Proposed update for the quantile function of the logistic distribution. [Closed]

> Username: WarrenWeckesser  
> Created at: 2025-08-10 14:45:51 UTC  
> Updated at: 2025-08-14 12:09:02 UTC  
> Closed at: 2025-08-14 12:09:02 UTC  
> Url: https://github.com/boostorg/math/issues/1296  

A while back I noticed that SciPy's `logit(p)` function lost precision near p=0.5.  I experimented with different formulations and found that `log1p(2*(p - 0.5)) - log1p(-2*(p - 0.5))` maintained high precision around p=0.5.  At the time, I didn't notice the formulation `2*atanh(2*p-1)`. (I'll probably propose we update the formula in SciPy soon.)  
  
I see that the quantile function of the logistic distribution in boost/math also uses the "naive" formula `log(p/(1-p))` , so it also loses precision around p=0.5. There is even a comment in the code about trying different formulations. The expression `2*atanh(2*p-1)` appears to maintain high precision except when `p` is small, where precision is lost in the expression `2*p-1`.  (Note: so far, I've been testing with double precision only.)  
  
I propose that the logistic quantile switch to the `atanh` formula for p > 0.25.  
  
I computed `logit(p)` on a grid on (0, 1) with the current quantile, with `atanh` on the entire interval, and with the proposed version (i.e. `atanh` for `p > 0.25`, the usual formula otherwise).  Here's a plot of the relative errors (reference values were computed with `mpmath`).  
  
<img width="764" height="381" alt="Image" src="https://github.com/user-attachments/assets/32e294ff-acd9-49b5-b4e1-6f10f24d47f7" />  
  
What do you think?

---

## Comment 1

> Username: WarrenWeckesser  
> Created at: 2025-08-10 23:05:29 UTC  
> Updated at: 2025-08-10 23:06:26 UTC  
> Url: https://github.com/boostorg/math/issues/1296#issuecomment-3172953076  

A more substantial change would be to add [`logit`](https://en.wikipedia.org/wiki/Logit) and the [logistic sigmoid function](https://en.wikipedia.org/wiki/Logistic_function) (sometimes called `expit`) to the special functions, and use them in the logistic distribution implementation.  This would reduce some existing code duplication.  Giving them a `Policy` parameter might be a step towards fixing gh-1294.

---

## Comment 2

> Username: mborland  
> Created at: 2025-08-11 07:47:06 UTC  
> Url: https://github.com/boostorg/math/issues/1296#issuecomment-3173615007  

> A more substantial change would be to add [`logit`](https://en.wikipedia.org/wiki/Logit) and the [logistic sigmoid function](https://en.wikipedia.org/wiki/Logistic_function) (sometimes called `expit`) to the special functions, and use them in the logistic distribution implementation. This would reduce some existing code duplication. Giving them a `Policy` parameter might be a step towards fixing [gh-1294](https://github.com/boostorg/math/issues/1294).  
  
Does `Scipy` have a `expit` implementation that could easily be ported into Boost.Math like the `logit` discussed above? This approach sounds good to me. I could put together the logic this week to have manual promotions based on policy. Something like:  
  
```  
Using std::atanh;  
Using policy_real_type = boost::math::tools::policy_promote_args_t<RealType, Policy>;  
Return static_cast<RealType>(2 * atanh(2 * static_cast<policy_real_type>(p) - 1));  
```  
  
It's a little ugly having all the casts, but then it actually does what it's supposed to do.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2025-08-11 09:02:24 UTC  
> Url: https://github.com/boostorg/math/issues/1296#issuecomment-3173843982  

Good catch, using the atan formula looks good to me as the `2p-1` is exact for p>0.5 and looses a single bit for 0.25<p<0.5.

---

## Comment 4

> Username: WarrenWeckesser  
> Created at: 2025-08-11 14:39:07 UTC  
> Url: https://github.com/boostorg/math/issues/1296#issuecomment-3175165627  

> Does Scipy have a `expit` implementation that could easily be ported into Boost.Math like the `logit` discussed above?  
  
The core `expit` calculation in SciPy (now actually in `xsf`) is simply `1 / (1 + std::exp(-x))`, so there is no need to port anything, just implement it.  That expression relies on the underflow and overflow behavior of `std::exp` for |x| large, but ideally the floating point exception flags and value of `errno` set because of this implementation detail should not leak out to a caller.  E.g. `expit(-1000.0)` should not report an overflow.  (It looks like the `xsf` implementation *does* leak these details currently.)  
  
FWIW: `expit` has always seemed like a horrible name to me.  Something like [`logistic`](https://en.wikipedia.org/wiki/Logistic_function) (or `logistic_sigmoid` if you're not into the whole brevity thing) would be more descriptive. [`logit`](https://en.wikipedia.org/wiki/Logit), while also not great, seems to be more widely used, so it might be best to stick with it.

---

## Comment 5

> Username: mborland  
> Created at: 2025-08-12 11:43:27 UTC  
> Url: https://github.com/boostorg/math/issues/1296#issuecomment-3178967102  

I believe I hit everything in one go in #1297. I went with `logsitic_sigmoid` as suggested because 1) `logistic` is already taken, and 2) if you google it the wiki page comes up unlike expit. Using structs already implemented in the policies we are able to get the type of the policy promoted (or not) `RealType`, rather than re-inventing the wheel.
