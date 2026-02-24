# #1159 Landau Integration testing [Merged]

> Username: jzmaddock  
> Created at: 2024-07-20 10:01:16 UTC  
> Updated at: 2024-08-12 06:44:10 UTC  
> Merged at: 2024-07-21 10:54:58 UTC  
> Closed at: 2024-07-21 10:54:58 UTC  
> Url: https://github.com/boostorg/math/pull/1159  

From https://github.com/boostorg/math/pull/1153

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-07-21 10:54:24 UTC  
> Url: https://github.com/boostorg/math/pull/1159#issuecomment-2241565404  

One failure is a drone network error, merging...

---

## Comment 2

> Username: dschmitz89  
> Created_at: 2024-08-10 12:26:48 UTC  
> Updated_at: 2024-08-10 12:29:19 UTC  
> Url: https://github.com/boostorg/math/pull/1159#issuecomment-2281458856  

Hey again, boost people. The Landau distribution is also requested in SciPy, thought we might once again build on top of what you merged here. Is there an online documentation of the current develop branch? I cannot find it in the CI logs at least: https://app.circleci.com/pipelines/github/boostorg/math/1982/workflows/0aca1d68-bb59-4b9d-bc01-3a4933919674/jobs/1963  
  
As this does not appear on the issue page, CC @jzmaddock @tk-yoshimura

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-08-10 14:09:42 UTC  
> Url: https://github.com/boostorg/math/pull/1159#issuecomment-2281979578  

Ummmm, noooo... but this should be quite readable in the meantime: https://github.com/boostorg/math/blob/develop/doc/distributions/landau.qbk

---

## Comment 4

> Username: tk-yoshimura  
> Created_at: 2024-08-11 01:23:16 UTC  
> Updated_at: 2024-08-11 01:25:10 UTC  
> Url: https://github.com/boostorg/math/pull/1159#issuecomment-2282340363  

Hello. @dschmitz89   
I have done a numerical evaluation of the Landau distribution.  
If you need help with the implementation in Scipy, please call me.  
  
Scipy requires additional implementations of logpdf, logcdf and logsf.  
The function value in left tail is terminated at the point where it can be seen as 0, but the approximation is valid up to -8.  
Input values smaller than -8 can use asymptotic expansion. The coefficients are listed in a numerical table in the report.  
https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L978-L983  
https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L1169-L1175  
https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L2229-L2234  
https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L2424-L2430  
  
I also think it would be inefficient for the Landau distribution to be instantiated and invoked element by array element like the cauchy distribution.    
https://github.com/scipy/scipy/blob/99c990758a11e1e35e1b9f5939e9c43651b28e7c/scipy/special/boost_special_functions.h#L591-L609

---

## Comment 5

> Username: dschmitz89  
> Created_at: 2024-08-11 07:38:32 UTC  
> Url: https://github.com/boostorg/math/pull/1159#issuecomment-2282657612  

> Hello. @dschmitz89 I have done a numerical evaluation of the Landau distribution. If you need help with the implementation in Scipy, please call me.  
>   
> Scipy requires additional implementations of logpdf, logcdf and logsf. The function value in left tail is terminated at the point where it can be seen as 0, but the approximation is valid up to -8. Input values smaller than -8 can use asymptotic expansion. The coefficients are listed in a numerical table in the report.  
>   
> https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L978-L983  
>   
>   
> https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L1169-L1175  
>   
>   
> https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L2229-L2234  
>   
>   
> https://github.com/boostorg/math/blob/66becad28e873477154c57d6721b274fe21db8a3/include/boost/math/distributions/landau.hpp#L2424-L2430  
>   
> I also think it would be inefficient for the Landau distribution to be instantiated and invoked element by array element like the cauchy distribution. https://github.com/scipy/scipy/blob/99c990758a11e1e35e1b9f5939e9c43651b28e7c/scipy/special/boost_special_functions.h#L591-L609  
  
Thanks for your answers. Actually, using boost results in very efficient code in SciPy as the Boost function is compiled as a NumPy ufunc using the machinery from `scipy.special`. That means it does not loop in Python but in C speed so we get (almost) the same speed as using Boost directly.  
  
`pdf` and `cdf` are enough for a first implementation. For the other methods, generic implementations are used as fallback (root finding for quantiles etc.) in SciPy. Accurate `logpdf` etc. are nice to have but not a must.

---

## Comment 6

> Username: tk-yoshimura  
> Created_at: 2024-08-12 06:06:34 UTC  
> Url: https://github.com/boostorg/math/pull/1159#issuecomment-2283170830  

Landau distribution is already implemented for pdf, cdf, quantile, mode, median, entropy.  
  
The following functions return function values for standard parameters.  
- landau_pdf_imp_prec  
- landau_cdf_imp_prec  
- landau_quantile_imp_prec  
- landau_mode_imp_prec  
- landau_median_imp_prec  
- landau_entropy_imp_prec  
  
If the `S0`, `S1` parameterization of the levy stable distribution in Scipy is to be used for the Landau distribution,   
it would be better to use the above function and apply the scaling and location parameters on the Scipy module.  
Note that in that case, no out-of-range checking of input values is performed.  
https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.levy_stable.html  
  
The case where &alpha; is a half-integer is also implemented.  
https://github.com/boostorg/math/pull/1163

---

## Comment 7

> Username: dschmitz89  
> Created_at: 2024-08-12 06:44:09 UTC  
> Url: https://github.com/boostorg/math/pull/1159#issuecomment-2283215395  

Awesome, good to hear that all those methods are implemented. Apparently I missed them when skimming the docs source code. If I create a PR, would you be able to review? Although oiriginally a physicist by training, I am not very familiar with this distribution. The different parametrizations are a PITA for scipy's current distribution infrastrucutre for example.

---
