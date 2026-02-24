# #1173 - New special functions: logarithm of incomplete gamma and beta functions [Open]

> Username: dschmitz89  
> Created at: 2024-08-08 18:53:00 UTC  
> Updated at: 2026-02-10 18:00:45 UTC  
> Url: https://github.com/boostorg/math/issues/1173  

The incomplete gamma and beta functions arise in a LOT of cumulative density functions. Very accurate implementations of them are quite common and also in Boost. What I have not seen yet are implementations of their logarithms. These would be very helpful to compute accurate logarithms of CDF and SFs. One example is an issue in SciPy where a user wants to compute the logcdf of the Poisson distribution for not even very extreme values: https://github.com/scipy/scipy/issues/8424. Here the logarithm of the incomplete regularized gamma function would come very much in handy.  
  
SciPy includes the logarithm of the normal distribution CDF [already](https://docs.scipy.org/doc/scipy/reference/generated/scipy.special.log_ndtr.html) but incomplete beta and gamma functions are of course a larger beast to kill.  
  
Is this something you would find worthwile for Boost?  
  
Edit: CC ing the special function wizards @steppi @WarrenWeckesser

---

## Comment 1

> Username: mborland  
> Created at: 2024-08-08 19:48:47 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-2276540920  

> The incomplete gamma and beta functions arise in a LOT of cumulative density functions. Very accurate implementations of them are quite common and also in Boost. What I have not seen yet are implementations of their logarithms. These would be very helpful to compute accurate logarithms of CDF and SFs. One example is an issue in SciPy where a user wants to compute the logcdf of the Poisson distribution for not even very extreme values: [scipy/scipy#8424](https://github.com/scipy/scipy/issues/8424). Here the logarithm of the incomplete regularized gamma function would come very much in handy.  
>   
> SciPy includes the logarithm of the normal distribution CDF [already](https://docs.scipy.org/doc/scipy/reference/generated/scipy.special.log_ndtr.html) but incomplete beta and gamma functions are of course a larger beast to kill.  
>   
> Is this something you would find worthwile for Boost?  
  
We certainly wouldn't turn them down. A while back @mdhaber asked about adding the `logpdf` and `logcdf`to the distribution so many were done (e.g. https://github.com/boostorg/math/blob/develop/include/boost/math/distributions/laplace.hpp#L235), but I think your proposal would help us both out. Right now the poisson dist simply uses: `log(cdf(...)))`.  
  
We have `logsumexp` here: https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/logsumexp.hpp, and continued fractions here: https://github.com/boostorg/math/blob/2a4351eb80a18c9abb5a40b96917df073f0939ae/include/boost/math/tools/simple_continued_fraction.hpp and here: https://github.com/boostorg/math/blob/2a4351eb80a18c9abb5a40b96917df073f0939ae/include/boost/math/tools/centered_continued_fraction.hpp depending on the route of implementation you're looking at.  
  
Let us know what kind of assistance you need.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-08-09 09:06:14 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-2277500066  

Internally, the incomplete gamma and beta's are mostly computed as the product of an asymptotic part and a power term (I say mostly, because there are many code paths depending on the arguments).  So in principle, calculating the log is relatively straightforward, it's just untangling all those code paths.  
  
I might be tempted to do what I did for 1F1, and pass a scaling term down through all the calculations - the scaling term is an integer (so no calculation error at least in principle), and we then scale the final result by e^N, or of course, take logs if we're going that way.  
  
BTW we already have the logpdf for this one correctly handled.  
  
I also wonder if your original issue was motivated by the desire to get the complement of the CDF?  ie he's asking for an accurate logcdf when the cdf is near 1.  Why?  To pass into expm1 to get the complement maybe?  But I digress!

---

## Comment 3

> Username: dschmitz89  
> Created at: 2024-08-12 06:49:19 UTC  
> Updated at: 2024-08-12 07:29:10 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-2283221680  

Thanks for your feedback, sounds like you agree that these would be useful. I am personally not qualified to help out as I am not a C++ or special function expert though. Let's see if we can find a champion for this one :).  
  
About the Poisson logpdf: do you list log methods in the distribution docs? For [Poisson](https://www.boost.org/doc/libs/1_85_0/libs/math/doc/html/math_toolkit/dist_ref/dists/poisson_dist.html), I cannot find it and the [general distribution page](https://www.boost.org/doc/libs/1_85_0/libs/math/doc/html/math_toolkit/dist_ref/nmp.html) does not list it either. In general if log methods are implemented, even generically without high precision, I would expect to find that info somewhere.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-08-12 08:36:49 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-2283396622  

I think @mborland added those, did the docs get updated at the same time?

---

## Comment 5

> Username: mborland  
> Created at: 2024-08-12 13:11:36 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-2283947612  

> I think @mborland added those, did the docs get updated at the same time?  
  
Apparently not so I'll get them merged in the next few days. The short of it is all distributions have logpdf and logcdf minimally through generic functions (e.g. log(cdf(...))) and some have one or both specialized.

---

## Comment 6

> Username: mborland  
> Created at: 2024-08-12 14:01:39 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-2284074293  

> Thanks for your feedback, sounds like you agree that these would be useful. I am personally not qualified to help out as I am not a C++ or special function expert though. Let's see if we can find a champion for this one :).  
>   
> About the Poisson logpdf: do you list log methods in the distribution docs? For [Poisson](https://www.boost.org/doc/libs/1_85_0/libs/math/doc/html/math_toolkit/dist_ref/dists/poisson_dist.html), I cannot find it and the [general distribution page](https://www.boost.org/doc/libs/1_85_0/libs/math/doc/html/math_toolkit/dist_ref/nmp.html) does not list it either. In general if log methods are implemented, even generically without high precision, I would expect to find that info somewhere.  
  
See: https://github.com/boostorg/math/pull/1176

---

## Comment 7

> Username: JacobHass8  
> Created at: 2025-12-09 23:38:04 UTC  
> Updated at: 2025-12-09 23:45:29 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3634735526  

If this is still open, I wouldn't mind working on it. I'm not exactly sure how to tackle it though. Would taking the logarithm of the continued fraction be sufficient?   
  
Apparently Fortran has an [implementation](https://stdlib.fortran-lang.org/page/specs/stdlib_specialfunctions_gamma.html#log_lower_incomplete_gamma-calculate-the-natural-logarithm-of-the-lower-incomplete-gamma-integral). Could I translate this to C++ and use this?

---

## Comment 8

> Username: mdhaber  
> Created at: 2025-12-09 23:57:51 UTC  
> Updated at: 2025-12-10 00:02:41 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3634774275  

> Could I translate this to C++ and use this?  
  
I'll let the Boost Math folks answer this directly, but the Fortran implementation is MIT-licensed, which is quite permissive.  
  
> Would taking the logarithm of the continued fraction be sufficient?  
  
Maybe if you use the log of the terms and use the logsumexp trick to do the arithmetic. I wrote a [private function](https://github.com/scipy/scipy/blob/62e20932546f2d95c78ec01c172f951835d006c2/scipy/stats/_continued_fraction.py#L68) for `scipy.stats` for evaluating generalized continued fractions. If you set `log=True`, then if your `a` and `b` callables evaluate the logarithm of the numerator and denominator coefficients, the result is the logarithm of the continued fraction, with all intermediate arithmetic performed in log-space. The function is vectorized and array API compatible (e.g. use CuPy backend), so you could use this to prototype something in Python and run extensive numerical tests to see how well it works.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2025-12-10 10:14:23 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3636338913  

@JacobHass8 : take a look at my reply above.  The existing code has a few different methods internally depending on what domain we're in, even taking logs we would need to emulate that to maintain accuracy and ensure we do do get convergence (eventually).  Most of the methods though have a series or CF part which we can always calculate (doesn't overflow - at least probably not!) and the asymptotic part in `regularised_gamma_prefix` which computes `(z^a)(e^-z)/tgamma(a)` for the regularized form and `(z^a)(e^-z)` in `full_igamma_prefix` for the non-regularized one.  Obviously these can be computed via logs to avoid overflow, but one word of warning: there is a danger, especially in the regularized prefix term, that the logs cancel out and the result is basically garbage.  In short there are no free lunches here, which is why I asked what the motivation was...  
  
Ah OK just looked at the original issue, so you want `log(Q[k+1, lambda])`, and the only time that taking the log of `Q[k+1, lambda]` fails is when the result is tiny, and in that special case everything goes through this function for integer k+1:  
  
```  
template <class T, class Policy>  
BOOST_MATH_GPU_ENABLED inline T finite_gamma_q(T a, T x, Policy const& pol, T* pderivative = 0)  
{  
   //  
   // Calculates normalised Q when a is an integer:  
   //  
   BOOST_MATH_STD_USING  
   T e = exp(-x);  
   T sum = e;  
   if(sum != 0)  
   {  
      T term = sum;  
      for(unsigned n = 1; n < a; ++n)  
      {  
         term /= n;  
         term *= x;  
         sum += term;  
      }  
   }  
   if(pderivative)  
   {  
      *pderivative = e * pow(x, a) / boost::math::unchecked_factorial<T>(itrunc(T(a - 1), pol));  
   }  
   return sum;  
}  
```  
  
Which should be trivial to evaluate as logs (ignore the derivative part in your case, and scale the sum by `e^-x` until the end and then take logs).  
  
I still wonder why anyone would case about probabilities that small in the real world, but no matter!  
  
Let me know if I'm missing anything, but a special case adaptation seems the best option rather than a fully general one here.

---

## Comment 10

> Username: JacobHass8  
> Created at: 2025-12-10 22:19:48 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3639194180  

> Ah OK just looked at the original issue, so you want `log(Q[k+1, lambda])`, and the only time that taking the log of `Q[k+1, lambda]` fails is when the result is tiny, and in that special case everything goes through this function for integer k+1:  
  
This is just for integer values of `k+1`, correct? I think that the idea was to generalize this to any values of $k \in \mathbb{R}$.  
  
> Which should be trivial to evaluate as logs (ignore the derivative part in your case, and scale the sum by `e^-x` until the end and then take logs).  
  
I think for integer `k+1` this would be an easy solution.   
  
> I still wonder why anyone would case about probabilities that small in the real world, but no matter!  
  
I've actually used these small probabilities to study [large deviations](https://en.wikipedia.org/wiki/Large_deviations_theory).

---

## Comment 11

> Username: JacobHass8  
> Created at: 2025-12-10 22:30:03 UTC  
> Updated at: 2025-12-10 22:30:33 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3639223370  

> Maybe if you use the log of the terms and use the logsumexp trick to do the arithmetic. I wrote a [private function](https://github.com/scipy/scipy/blob/62e20932546f2d95c78ec01c172f951835d006c2/scipy/stats/_continued_fraction.py#L68) for `scipy.stats` for evaluating generalized continued fractions. If you set `log=True`, then if your `a` and `b` callables evaluate the logarithm of the numerator and denominator coefficients, the result is the logarithm of the continued fraction, with all intermediate arithmetic performed in log-space. The function is vectorized and array API compatible (e.g. use CuPy backend), so you could use this to prototype something in Python and run extensive numerical tests to see how well it works.  
  
Using your code, I've been able to get some promising results. Here's an example script I made and the output:   
<details>  
  
<summary>Example</summary>  
  
```python  
import numpy as np  
from scipy.stats._continued_fraction import _continued_fraction as cf  
from scipy.special import gammaincc, gamma  
import warnings  
  
def gammaincc_series(s, z, size=100):  
    # Return series an, bn for incomplete gamma function at (s, z)  
    an = np.zeros(size)  
    bn = np.zeros(size)  
    an[1] = 1  
    bn[0] = 0  
    bn[1:] = 2 * np.arange(0, size-1) + 1 + z - s  
    an[2:] = np.arange(1, size-1) * (s - np.arange(1, size-1))  
    return an, bn  
  
s = 10  
z = 1000  
a, b = gammaincc_series(s, z, 100)  
  
# Stop evaluation of series when an = 0  
zero_idx = np.where(a == 0)[0][1]  
a = a[:zero_idx + 1]  
b = b[:zero_idx + 1]  
  
# Calculate without using log arithmetic  
res = cf(a=lambda n: a[n], b=lambda n: b[n], maxiter=len(b) - 1, log=False)  
gammaincc_val = z ** s * np.exp(-z) * res.f  
with np.errstate(divide='ignore'):  
    print('Without using log arithmetic:', np.log(gammaincc_val))  
    # Scipy implementation differs from wikipedia by factor of gamma(s)  
    print('Current scipy.special implementation:', np.log(gammaincc(s, z) * gamma(s)))  
  
# When log=True, get complex warning and divide by zero in log  
with warnings.catch_warnings():  
    warnings.simplefilter("ignore")  
    a = np.log(a)  
    b = np.log(b)  
    res = cf(a=lambda n: a[n], b=lambda n: b[n], maxiter=len(b) - 1, log=True)  
gammaincc_val = res.f + s * np.log(z) - z   
print('Using log arithmetic:', gammaincc_val)  
```  
where only the log arithmetic version returns a number:  
```  
Without using log arithmetic: -inf  
Current scipy.special implementation: -inf  
Using log arithmetic: -937.8211708900747  
```  
For reference, this matches Mathematica.  
</details>  
  
However, there are issues when $a_n$ or $b_n$ are negative. When this happens I can't take the log to put into the `_continued_fraction` function. I'm sure there's an easy fix, but I haven't dug into the code just yet. I'm thinking this might be the way to go.

---

## Comment 12

> Username: mdhaber  
> Created at: 2025-12-10 23:07:11 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3639318664  

I think `_continued_fraction` will "just work" with complex numbers. Use a complex dtype and the imaginary component of the log a negative number will be $\pi$ (or some multiple); e.g.  
```  
import numpy  as np  
np.log(-1 + 0j)  # np.complex128(3.141592653589793j)  
```

---

## Comment 13

> Username: JacobHass8  
> Created at: 2025-12-10 23:40:54 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3639389058  

> I think `_continued_fraction` will "just work" with complex numbers. Use a complex dtype and the imaginary component of the log a negative number will be π (or some multiple); e.g.  
  
Yeah, that's such a simple solution! Do we still need a C++ implementation of this or would this Python implementation be adequate?

---

## Comment 14

> Username: mdhaber  
> Created at: 2025-12-10 23:53:32 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3639414683  

We'd want C++. The goal would be for it to make its way into XSF, then into other backends besides SciPy, and then hopefully it could be part of the array API [special function extension](data-apis/array-api#725).

---

## Comment 15

> Username: JacobHass8  
> Created at: 2025-12-11 17:11:20 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3642906593  

Great, I'll work on translating your code to C++. Thanks for the help!

---

## Comment 16

> Username: mdhaber  
> Created at: 2025-12-11 17:14:38 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3642917443  

I'm not sure I'd necessarily recommend that - please follow @jzmaddock and @mborland's lead here. Boost already has tools for evaluating continued fractions - maybe they'd like those enhanced to work in log-space?

---

## Comment 17

> Username: JacobHass8  
> Created at: 2025-12-11 17:22:38 UTC  
> Updated at: 2025-12-11 17:26:15 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3642964500  

Oh, that makes things easier then. I'll poke around in the repository and see what I can find.   
  
Yeah, here's a list of examples already https://github.com/boostorg/math/blob/develop/example/continued_fractions.cpp

---

## Comment 18

> Username: mdhaber  
> Created at: 2026-02-07 18:00:27 UTC  
> Updated at: 2026-02-07 18:00:34 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3864989168  

I'm looking forward to using the new incomplete gamma functions in SciPy! I know that those were a lot of work, so I didn't mean to press my luck, but I wanted to mention that it would be terrific to have the logarithms of the incomplete beta functions in the same release. Thank you!

---

## Comment 19

> Username: JacobHass8  
> Created at: 2026-02-10 01:49:10 UTC  
> Updated at: 2026-02-10 05:44:52 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3874861209  

I've just started to look at the incomplete beta function again. I don't think the implementation will be all that difficult. The issue is in this [line](https://github.com/boostorg/math/blob/c42193db07aef6f69a439815a9310729282587c7/include/boost/math/special_functions/beta.hpp#L1628C4-L1628C84) which reads  
```C++  
return invert ? (normalised ? 1 : boost::math::beta(a, b, pol)) - fract : fract;  
```  
When `fract` is close to 0, taking the invert and normalized is true we are taking the log of `1-fract`. When `fract` is close to 0, we should take `log1p(-fract)` rather than `log(1-fract)`. This should fix our issues when `ibeta` is close to `1`.   
  
I don't know the best way of implementing this change though. Currently, I duplicated all the code for `ibeta_imp` and changed the line above to   
```C++  
if (invert){  
    if (normalised){  
        return std::log1p(-fract);  
    }  
    else{  
        return log(boost::math::beta(a, b, pol) - fract);  
    }  
}  
else{  
    return log(fract);  
}  
```  
Other than that, all the logic is the same. Should I open a new PR with the new code and we can iterate from there?   
  
@mborland or @jzmaddock do you have any ideas on how to go about this? The `invert` variable is really confusing to me because it changes in different cases. The following code gives the desired precision in the both tails, but is just a copy/paste of `ibeta_imp` (without any of the edge case handling). I just don't know how to disentangle `invert` from `ibeta_imp`. Essentially, we want `inv=true` in one tail and `inv=false` in the other. I'm not sure that any of this makes sense as I'm not explaining it well.   
<details><summary>Details</summary>  
<p>  
  
```C++  
template <class T, class Policy>  
T libeta_imp(T a, T b, T x, const Policy& pol)  
{  
    constexpr auto function = "boost::math::ibeta<%1%>(%1%, %1%, %1%)";  
    typedef typename lanczos::lanczos<T, Policy>::type lanczos_type;  
    BOOST_MATH_STD_USING // for ADL of std math functions.  
  
    bool inv = false;  
    bool normalised = true;  
  
    BOOST_MATH_INSTRUMENT_VARIABLE(a);  
    BOOST_MATH_INSTRUMENT_VARIABLE(b);  
    BOOST_MATH_INSTRUMENT_VARIABLE(x);  
    BOOST_MATH_INSTRUMENT_VARIABLE(inv);  
    BOOST_MATH_INSTRUMENT_VARIABLE(normalised);  
  
    T* p_derivative = static_cast<T*>(nullptr);  
  
    bool invert = inv;  
    T fract;  
    T y = 1 - x;  
  
    BOOST_MATH_ASSERT((p_derivative == 0) || normalised);  
      
    if(BOOST_MATH_GPU_SAFE_MIN(a, b) <= 1)  
    {  
        if(x > 0.5)  
        {  
            BOOST_MATH_GPU_SAFE_SWAP(a, b);  
            BOOST_MATH_GPU_SAFE_SWAP(x, y);  
            invert = !invert;  
            BOOST_MATH_INSTRUMENT_VARIABLE(invert);  
        }  
        if(BOOST_MATH_GPU_SAFE_MAX(a, b) <= 1)  
        {  
            // Both a,b < 1:  
            if((a >= BOOST_MATH_GPU_SAFE_MIN(T(0.2), b)) || (pow(x, a) <= 0.9))  
            {  
                if(!invert)  
                {  
                fract = ibeta_series(a, b, x, T(0), lanczos_type(), normalised, p_derivative, y, pol);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                fract = -(normalised ? 1 : boost::math::beta(a, b, pol));  
                invert = false;  
                fract = -ibeta_series(a, b, x, fract, lanczos_type(), normalised, p_derivative, y, pol);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
            }  
            else  
            {  
                BOOST_MATH_GPU_SAFE_SWAP(a, b);  
                BOOST_MATH_GPU_SAFE_SWAP(x, y);  
                invert = !invert;  
                if(y >= 0.3)  
                {  
                if(!invert)  
                {  
                    fract = ibeta_series(a, b, x, T(0), lanczos_type(), normalised, p_derivative, y, pol);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                    fract = -(normalised ? 1 : boost::math::beta(a, b, pol));  
                    invert = false;  
                    fract = -ibeta_series(a, b, x, fract, lanczos_type(), normalised, p_derivative, y, pol);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                }  
                else  
                {  
                // Sidestep on a, and then use the series representation:  
                T prefix;  // LCOV_EXCL_LINE  
                if(!normalised)  
                {  
                    prefix = rising_factorial_ratio(T(a+b), a, 20);  
                }  
                else  
                {  
                    prefix = 1;  
                }  
                fract = ibeta_a_step(a, b, x, y, 20, pol, normalised, p_derivative);  
                if(!invert)  
                {  
                    fract = beta_small_b_large_a_series(T(a + 20), b, x, y, fract, prefix, pol, normalised);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                    fract -= (normalised ? 1 : boost::math::beta(a, b, pol));  
                    invert = false;  
                    fract = -beta_small_b_large_a_series(T(a + 20), b, x, y, fract, prefix, pol, normalised);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                }  
            }  
        }  
        else  
        {  
            // One of a, b < 1 only:  
            if((b <= 1) || ((x < 0.1) && (pow(b * x, a) <= 0.7)))  
            {  
                if(!invert)  
                {  
                fract = ibeta_series(a, b, x, T(0), lanczos_type(), normalised, p_derivative, y, pol);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                fract = -(normalised ? 1 : boost::math::beta(a, b, pol));  
                invert = false;  
                fract = -ibeta_series(a, b, x, fract, lanczos_type(), normalised, p_derivative, y, pol);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
            }  
            else  
            {  
                BOOST_MATH_GPU_SAFE_SWAP(a, b);  
                BOOST_MATH_GPU_SAFE_SWAP(x, y);  
                invert = !invert;  
  
                if(y >= 0.3)  
                {  
                if(!invert)  
                {  
                    fract = ibeta_series(a, b, x, T(0), lanczos_type(), normalised, p_derivative, y, pol);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                    fract = -(normalised ? 1 : boost::math::beta(a, b, pol));  
                    invert = false;  
                    fract = -ibeta_series(a, b, x, fract, lanczos_type(), normalised, p_derivative, y, pol);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                }  
                else if(a >= 15)  
                {  
                if(!invert)  
                {  
                    fract = beta_small_b_large_a_series(a, b, x, y, T(0), T(1), pol, normalised);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                    fract = -(normalised ? 1 : boost::math::beta(a, b, pol));  
                    invert = false;  
                    fract = -beta_small_b_large_a_series(a, b, x, y, fract, T(1), pol, normalised);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                }  
                else  
                {  
                // Sidestep to improve errors:  
                T prefix;  // LCOV_EXCL_LINE  
                if(!normalised)  
                {  
                    prefix = rising_factorial_ratio(T(a+b), a, 20);  
                }  
                else  
                {  
                    prefix = 1;  
                }  
                fract = ibeta_a_step(a, b, x, y, 20, pol, normalised, p_derivative);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                if(!invert)  
                {  
                    fract = beta_small_b_large_a_series(T(a + 20), b, x, y, fract, prefix, pol, normalised);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                    fract -= (normalised ? 1 : boost::math::beta(a, b, pol));  
                    invert = false;  
                    fract = -beta_small_b_large_a_series(T(a + 20), b, x, y, fract, prefix, pol, normalised);  
                    BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                }  
            }  
        }  
    }  
    else  
    {  
        // Both a,b >= 1:  
        T lambda;  // LCOV_EXCL_LINE  
        if(a < b)  
        {  
            lambda = a - (a + b) * x;  
        }  
        else  
        {  
            lambda = (a + b) * y - b;  
        }  
        if(lambda < 0)  
        {  
            BOOST_MATH_GPU_SAFE_SWAP(a, b);  
            BOOST_MATH_GPU_SAFE_SWAP(x, y);  
            invert = !invert;  
            BOOST_MATH_INSTRUMENT_VARIABLE(invert);  
        }  
  
        if(b < 40)  
        {  
            if((floor(a) == a) && (floor(b) == b) && (a < static_cast<T>((boost::math::numeric_limits<int>::max)() - 100)) && (y != 1))  
            {  
                // relate to the binomial distribution and use a finite sum:  
                T k = a - 1;  
                T n = b + k;  
                fract = binomial_ccdf(n, k, x, y, pol);  
                if(!normalised)  
                fract *= boost::math::beta(a, b, pol);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
            }  
            else if(b * x <= 0.7)  
            {  
                if(!invert)  
                {  
                fract = ibeta_series(a, b, x, T(0), lanczos_type(), normalised, p_derivative, y, pol);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
                else  
                {  
                fract = -(normalised ? 1 : boost::math::beta(a, b, pol));  
                invert = false;  
                fract = -ibeta_series(a, b, x, fract, lanczos_type(), normalised, p_derivative, y, pol);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
                }  
            }  
            else if(a > 15)  
            {  
                // sidestep so we can use the series representation:  
                int n = itrunc(T(floor(b)), pol);  
                if(n == b)  
                --n;  
                T bbar = b - n;  
                T prefix; // LCOV_EXCL_LINE  
                if(!normalised)  
                {  
                prefix = rising_factorial_ratio(T(a+bbar), bbar, n);  
                }  
                else  
                {  
                prefix = 1;  
                }  
                fract = ibeta_a_step(bbar, a, y, x, n, pol, normalised, static_cast<T*>(nullptr));  
                fract = beta_small_b_large_a_series(a,  bbar, x, y, fract, T(1), pol, normalised);  
                fract /= prefix;  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
            }  
            else if(normalised)  
            {  
                // The formula here for the non-normalised case is tricky to figure  
                // out (for me!!), and requires two pochhammer calculations rather  
                // than one, so leave it for now and only use this in the normalized case....  
                int n = itrunc(T(floor(b)), pol);  
                T bbar = b - n;  
                if(bbar <= 0)  
                {  
                --n;  
                bbar += 1;  
                }  
                fract = ibeta_a_step(bbar, a, y, x, n, pol, normalised, static_cast<T*>(nullptr));  
                fract += ibeta_a_step(a, bbar, x, y, 20, pol, normalised, static_cast<T*>(nullptr));  
                if(invert)  
                fract -= 1;  // Note this line would need changing if we ever enable this branch in non-normalized case  
                fract = beta_small_b_large_a_series(T(a+20),  bbar, x, y, fract, T(1), pol, normalised);  
                if(invert)  
                {  
                fract = -fract;  
                invert = false;  
                }  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
            }  
            else  
            {  
                fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative);  
                BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
            }  
        }  
        else  
        {  
            // a and b both large:  
            bool use_asym = false;  
            T ma = BOOST_MATH_GPU_SAFE_MAX(a, b);  
            T xa = ma == a ? x : y;  
            T saddle = ma / (a + b);  
            T powers = 0;  
            if ((ma > 1e-5f / tools::epsilon<T>()) && (ma / BOOST_MATH_GPU_SAFE_MIN(a, b) < (xa < saddle ? 2 : 15)))  
            {  
                if (a == b)  
                use_asym = true;  
                else  
                {  
                powers = exp(log(x / (a / (a + b))) * a + log(y / (b / (a + b))) * b);  
                if (powers < tools::epsilon<T>())  
                    use_asym = true;  
                }  
            }  
            if(use_asym)  
            {  
                fract = ibeta_large_ab(a, b, x, y, invert, normalised, pol);  
                if (fract * tools::epsilon<T>() < powers)  
                {  
                // Erf approximation failed, correction term is too large, fall back:  
                fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative);  
                }  
                else  
                invert = false;  
            }  
            else  
                fract = ibeta_fraction2(a, b, x, y, pol, normalised, p_derivative);  
                  
            BOOST_MATH_INSTRUMENT_VARIABLE(fract);  
        }  
    }  
    if(p_derivative)  
    {  
        if(*p_derivative < 0)  
        {  
            *p_derivative = ibeta_power_terms(a, b, x, y, lanczos_type(), true, pol);  
        }  
        T div = y * x;  
  
        if(*p_derivative != 0)  
        {  
            if((tools::max_value<T>() * div < *p_derivative))  
            {  
                // overflow, return an arbitrarily large value:  
                *p_derivative = tools::max_value<T>() / 2;   // LCOV_EXCL_LINE  Probably can only get here with denormalized x.  
            }  
            else  
            {  
                *p_derivative /= div;  
            }  
        }  
    }  
    if (invert){  
        if (normalised){  
            return std::log1p(-fract);  
        }  
        else{  
            return log(boost::math::beta(a, b, pol) - fract);  
        }  
    }  
    else{  
        return log(fract);  
    }  
} // template <class T, class Lanczos>T ibeta_imp(T a, T b, T x, const Lanczos& l)  
```  
  
</p>  
</details>

---

## Comment 20

> Username: jzmaddock  
> Created at: 2026-02-10 16:20:37 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3879035723  

Thanks for looking at this: given the complexity of ibeta_imp and it's selection logic, I wonder if we should just add another parameter to it (bool islog = false) and return either the log or the value depending on the parameter value?  Or would that make the function just too complex?  
  
With regard to the invert parameter: it should mostly end up false if we're in one tail and true if we're in the other anyway as basically we compute whichever is closer to zero and then subtract from one at the end if we computed the "wrong" one.  Hope that makes sense!

---

## Comment 21

> Username: JacobHass8  
> Created at: 2026-02-10 18:00:45 UTC  
> Url: https://github.com/boostorg/math/issues/1173#issuecomment-3879834467  

> Thanks for looking at this: given the complexity of ibeta_imp and it's selection logic, I wonder if we should just add another parameter to it (bool islog = false) and return either the log or the value depending on the parameter value? Or would that make the function just too complex?  
  
That seems like the easiest solution to me. Everything else that I've tried would require a major rewrite of `ibeta_imp`. I'll give it a shot and open a new PR when I have something sensible. Thanks for the help!
