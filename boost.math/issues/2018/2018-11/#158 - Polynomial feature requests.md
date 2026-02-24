# #158 - Polynomial feature requests [Closed]

> Username: NAThompson  
> Created at: 2018-11-14 22:35:55 UTC  
> Updated at: 2018-12-04 21:36:12 UTC  
> Closed at: 2018-12-04 21:36:12 UTC  
> Url: https://github.com/boostorg/math/issues/158  

Couple of things I could use for working with polynomials:  
  
1) Instead of `p.evaluate(x)`, it would be nice to be able to simply write `p(x)`. That would allow passing the polynomial directly into various functionals, like root-finding and quadrature.  
  
2) It would be nice to have a move constructor from `std::vector<T>`, since the storage format is `std::vector<T>` and after you've constructed the coefficients and passed them into the polynomial, you don't really need them anymore.  
  
3) I'd like to be able to write `polynomial<Real> q = p.prime()` to get the derivative of the polynomial.  
  
4) It would be nice to be able to write `auto r = p.roots()`. Well, that's a challenging problem since it's ill-conditioned. But Trefethen has shown that if you represent a polynomial as a Chebyshev series, then there exists a fast algorithm to compute the roots. (More info [here](https://cfwebprod.sandia.gov/cfdocs/CompResearch/docs/609841.pdf), [here](https://www.pvamu.edu/mathematics/wp-content/uploads/sites/49/22_R982_AAM_Solary_MS_121016_V12_I2_Dec_2017.pdf), and [here](https://docs.scipy.org/doc/numpy-1.13.0/reference/generated/numpy.polynomial.chebyshev.chebroots.html#numpy.polynomial.chebyshev.chebroots).)  
  
5) `p.integrate()`.  
  
6) I didn't know that this had a `.chebyshev()` method, so it'd be nice to know if it was compatible with the Chebyshev transform.  
  
7) We should be able to template on a complex type.  
  
I'm writing this just to remind myself of features I want as I use them; most of these are trivial so I'll try to write them as I encounter them.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2018-11-22 10:49:39 UTC  
> Url: https://github.com/boostorg/math/issues/158#issuecomment-440991182  

1. Agreed.  
2. Agreed.  
3. I started work on this a couple of years ago... I'll check if I still have it.  
4. Sounds cool.  
5. Yes.  
6. Good question.  
7. And matrices! :)

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-12-04 21:36:12 UTC  
> Url: https://github.com/boostorg/math/issues/158#issuecomment-444267692  

For reference, all of this is done except 4 and 6. 4 looks like it requires serious linear algebra, which would probably require a dependency on Eigen, so it may well be a misfeature. Closing this for now.
