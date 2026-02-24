# #303 - Feature Wishlist [Open]

> Username: NAThompson  
> Created at: 2020-01-13 17:04:50 UTC  
> Updated at: 2025-03-28 14:46:34 UTC  
> Url: https://github.com/boostorg/math/issues/303  

- Modified Clenshaw recurrence for all special functions governed by 3-term recurrence relations.  
- Higher dimensional interpolation (Akima has a natural generalization to higher dimensions, but there's some work on getting the API correct.)  
- Prolate spheroidal wavefunctions  
- Higher dimensional quadrature; sparse grids for medium dimensionality, extensions of Gaussian quadrature in lower dimensions.  
- Wavelet transforms, shearlets  
- Bivariate interpolation and quadrature at the [Padua points](https://www.math.unipd.it/~marcov/pdf/poster5ecm.pdf)  
  
What else is a natural fit for the library, and is useful?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-01-13 18:53:10 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-573814617  

> What else is a natural fit for the library, and is useful?  
  
* Make further progress on zeta, phi and friends (Lerch phi transcendent, polylog, and more).  
* Extend constants to higher precision such as 10,000 decimal digits and get them tested.  
* Evaluate recent progress in the literature on Bernoulli number generation (can we save memory and/or time compared with extracting Bn from tangent numbers?).

---

## Comment 2

> Username: ckormanyos  
> Created at: 2020-01-13 19:26:01 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-573828731  

> What else is a natural fit for the library, and is useful?  
  
* Legendre functions and associated Legendre functions of arbitrary (non-integer real) order and degree.  
* More special functions of complex argument, particularly on the unit disk. Gamma should be a reasonable place to start. Perhaps complex orthogonal polynomials if these are calculated from straightforward recursion.  
* FFT

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-01-19 16:46:19 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-576023211  

Friends, if you don't mind, I'm going to clean up this thread since it kinda got off topic and many of the off-topic issues have been resolved.

---

## Comment 4

> Username: octopus-prime  
> Created at: 2020-01-19 18:10:39 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-576030311  

- fixed sized polynomials without allocation  
- corresponding polynomial solvers, e.g. analytic, bairstow, companion matrix

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-01-19 18:30:33 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-576032524  

@octopus-prime : Yup. Also that reminds me: Jenkins-Traub.

---

## Comment 6

> Username: cosurgi  
> Created at: 2020-02-06 22:23:07 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-583140413  

Did you notice recent discussion about [FFT in mailing list](https://lists.boost.org/Archives/boost/2020/02/248146.php)? I think it would be great if math supported FFT for all multiprecision types, especially [quad-double, which is currently in the works](https://github.com/boostorg/multiprecision/issues/184). And please note that it's not possible to use libfftw as a backend for quad-double.

---

## Comment 7

> Username: sebweb3r  
> Created at: 2020-02-07 12:50:14 UTC  
> Updated at: 2020-02-07 12:50:57 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-583376328  

first and second derivative for interpolations (exist sometimes)  
2d interpolation

---

## Comment 8

> Username: NAThompson  
> Created at: 2020-02-07 13:34:53 UTC  
> Updated at: 2020-02-07 13:35:34 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-583391196  

I think every interpolator has the first derivative implemented. Second derivatives of the quintic B-spline are implemented. Other interpolators have the wrong spectral properties and perform poorly on second derivatives.  
  
2D interpolation is coming! (Akima + pchip)

---

## Comment 9

> Username: NAThompson  
> Created at: 2020-02-07 13:40:46 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-583393410  

@cosurgi : Totally agree it would be useful, but I really like FFTW and it satisfies all my needs, so other things are a higher priority. I don't know who has the skillset to take on such a difficult task . . .

---

## Comment 10

> Username: mrityunjay-tripathi  
> Created at: 2020-02-24 16:47:20 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-590428944  

> What else is a natural fit for the library, and is useful?  
  
* Dirichlet Distribution #317

---

## Comment 11

> Username: mborland  
> Created at: 2023-01-24 17:16:40 UTC  
> Updated at: 2023-04-13 11:44:17 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-1402302732  

From discussion with the SciPy Maintainers:  
  
- Symplectic ODE solver https://github.com/scipy/scipy/issues/12690  
- logcdf for statistical distributions #946   
- entropy for statistical distributions  
- Add support for complex arguments to appropriate statistical distributions  
- Make develop branch work with Scipy https://github.com/scipy/scipy/pull/17432

---

## Comment 12

> Username: WarrenWeckesser  
> Created at: 2025-01-23 00:31:32 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-2608576903  

I think the [generalized extreme value distribution](https://en.wikipedia.org/wiki/Generalized_extreme_value_distribution) would be useful addition to the distributions collection.  
  
I'm working on implementing it.  I can submit a PR once I get it working, if there is interest.

---

## Comment 13

> Username: mborland  
> Created at: 2025-01-23 13:32:11 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-2609819063  

> I think the [generalized extreme value distribution](https://en.wikipedia.org/wiki/Generalized_extreme_value_distribution) would be useful addition to the distributions collection.  
>   
> I'm working on implementing it. I can submit a PR once I get it working, if there is interest.  
  
We certainly won't say no. Let us know if you have any questions, but I think you're pretty familiar with our internals and testing.

---

## Comment 14

> Username: WarrenWeckesser  
> Created at: 2025-01-23 21:03:24 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-2611012143  

Thanks.  I already have a question or two.  Any preference for using the boost developer's mailing list (i.e. https://lists.boost.org/mailman/listinfo.cgi/boost) vs a new github issue?

---

## Comment 15

> Username: mborland  
> Created at: 2025-01-23 21:05:24 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-2611016052  

> Thanks. I already have a question or two. Any preference for using the boost developer's mailing list (i.e. https://lists.boost.org/mailman/listinfo.cgi/boost) vs a new github issue?  
  
A GitHub issue would be better.

---

## Comment 16

> Username: matwey  
> Created at: 2025-03-28 14:46:33 UTC  
> Url: https://github.com/boostorg/math/issues/303#issuecomment-2761584301  

> What else is a natural fit for the library, and is useful?  
  
Single exponential transform quadratures (I know that there are double exponential quadratures in the library, but this research https://link.springer.com/article/10.1007/s00211-008-0195-1 claims that SE are capable for wider class by a price of slower convergence rate. It means that for some integrals where DE works poorly SE can potentially works satisfactorily)
