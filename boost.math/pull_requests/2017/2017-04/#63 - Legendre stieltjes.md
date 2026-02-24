# #63 Legendre stieltjes [Merged]

> Username: NAThompson  
> Created at: 2017-04-30 22:00:41 UTC  
> Updated at: 2017-05-12 02:25:16 UTC  
> Merged at: 2017-05-10 17:14:01 UTC  
> Closed at: 2017-05-10 17:14:01 UTC  
> Url: https://github.com/boostorg/math/pull/63  

Legendre-Stieltjes polynomials are orthogonal with respect to the oscillatory measure defined by the Legendre polynomials. This property allows generation of Konrod extensions to Gaussian quadrature rules, which in turn allows for error estimates with abscissa reuse.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-05-01 17:58:38 UTC  
> Url: https://github.com/boostorg/math/pull/63#issuecomment-298388026  

Small housekeeping observation: don't forget to put your copyright, and the Boost licence on the top of each new file.  The existing headers should give you the form of the magical invocation ;)

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2017-05-02 20:55:54 UTC  
> Url: https://github.com/boostorg/math/pull/63#issuecomment-298758523  

Nice! Thanks for contributing.  
  
  
  
    On Monday, May 1, 2017 12:00 AM, Nick <notifications@github.com> wrote:  
   
  
 Legendre-Stieltjes polynomials are orthogonal with respect to the oscillatory measure defined by the Legendre polynomials. This property allows generation of Konrod extensions to Gaussian quadrature rules, which in turn allows for error estimates with abscissa reuse.  
You can view, comment on, or merge this pull request online at:  
  https://github.com/boostorg/math/pull/63  
Commit Summary  
     
   - Derivative of Legendre polynomials of the first kind. Used to generate the weights of Gaussian quadratures.  
   - Zeros of Legendre polynomials. This uses a root bracketing given by Szego with an asymptotic by Tricomi to get a domain and an initial guess for the root, then refines it via Newton's method.  
   - Return all zeros of the Legendre polynomial at once; there seems to be no benefit to having the user calculate them one at a time.  
   - Legendre-Stieltjes polynomials.  
  
File Changes  
     
   -  M doc/sf/legendre.qbk (121)   
   -  A doc/sf/legendre_stieltjes.qbk (65)   
   -  A example/legendre_stieltjes_example.cpp (94)   
   -  M include/boost/math/special_functions/legendre.hpp (163)   
   -  A include/boost/math/special_functions/legendre_stieltjes.hpp (229)   
   -  M include/boost/math/special_functions/math_fwd.hpp (14)   
   -  A test/legendre_stieltjes_test.cpp (135)   
   -  M test/test_legendre.cpp (13)   
   -  M test/test_legendre.hpp (183)   
  
Patch Links:  
     
   - https://github.com/boostorg/math/pull/63.patch  
   - https://github.com/boostorg/math/pull/63.diff  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---
