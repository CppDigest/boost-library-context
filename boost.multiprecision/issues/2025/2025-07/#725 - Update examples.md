# #725 - Update examples [Open]

> Username: ckormanyos  
> Created at: 2025-07-14 07:49:44 UTC  
> Updated at: 2025-07-14 07:50:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/725  

We can upgrade some of the examples, as partially motivated by discussion in #718.  
  
- [ ] Having one example that demonstrates the using trick when necessary, would be good.  
- [ ] The [Jahnke-Emden-Lambda example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/jel.html) makes use of `using`. But we could emphasize it even more.  
- [ ] The [Calculating a Derivative example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/nd.html) could make much better use of `using`, particularly in the calls of the derivative functor(s).  
- [ ] The [Calculating an Integral example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/gi.html) could be made much more generic. It seems to already be relying on ADL and does not use `using`. I'm not even sure it is set up properly for `float`, `double` or `long double`?  
- [ ] Looking at [Polynomial Evaluation](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/poly_eg.html) I have evolved in the past 20 years or so. These days, I tend to prefer Pade-like expansions for these in order to reduce the coefficient list(s). These still require polynomial evaluation for the numerator and denominator in the Pade approximation.  
- [x] In the [Gause-Laguerre example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/gauss_lagerre_quadrature.html), we could post the [full link](https://www.wolframalpha.com/input?i=Fit%5B%7B%7B21.0%2C+3.5%7D%2C+%7B51.0%2C+11.1%7D%2C+%7B101.0%2C+22.5%7D%2C+%7B201.0%2C+46.8%7D%7D%2C+%7B1%2C+d%2C+d%5E2%7D%2C+d%5D+FullSimplify%5B%25%5D) to the Wolfram alpha code for the guess at the numbers of coefficients.
