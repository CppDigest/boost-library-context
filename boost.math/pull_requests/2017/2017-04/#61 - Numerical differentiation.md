# #61 Numerical differentiation [Closed]

> Username: NAThompson  
> Created at: 2017-04-04 23:48:56 UTC  
> Updated at: 2018-12-28 01:38:08 UTC  
> Closed at: 2017-05-03 00:30:43 UTC  
> Url: https://github.com/boostorg/math/pull/61  

Hi guys,  
  
I hope you think this PR is cool; I've been running into some problems where writing a custom differentiation routine for each function I encounter is painful and not worth the time investment, so I wrote these two functions which differentiate generic functions.  
  
The complex step differentiation is amazing; it should allow any special function in boost::math that can be evaluated at complex arguments to be differentiated on the real line to machine precision.  
  
The finite-difference formulas are less impressive but nonetheless the correct algorithm for this is delicate enough that I think people will like punting the details to boost::math.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2017-04-04 23:50:17 UTC  
> Url: https://github.com/boostorg/math/pull/61#issuecomment-291676217  

Sorry I should've forked off `develop`, not my own branch. Only the last commit is relevant to this PR.

---

## Comment 2

> Username: pabristow  
> Created_at: 2017-04-05 11:32:59 UTC  
> Url: https://github.com/boostorg/math/pull/61#issuecomment-291833542  

Looks hyper-cool ;-)  
  
Now I've abandoned a wild goose chase, I hope to finish Lambert_w function soonish and then work on refining documentation.   Is/are there example(s) of using the differentiation methods?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2017-04-05 15:23:34 UTC  
> Url: https://github.com/boostorg/math/pull/61#issuecomment-291896915  

Hi Paul,  
  
The reason I wrote it is because I needed to differentiate Daubechies wavelets to evaluate expectation values of kinetic energy. I'm hoping to get a Daubechies wavelet commit in soon enough, but when I get that done I think it would be a nice example.  
  
Another example is computing the weights of Gaussian quadrature. Assuming we can evaluate the Legendre polynomials at complex argument, then the weights can be computed to high accuracy using the complex step differentiation. I'll try to commit an example in a couple days.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2017-05-03 00:30:43 UTC  
> Url: https://github.com/boostorg/math/pull/61#issuecomment-298797947  

Since I branched off the wrong commit, this will generate a bunch of merge conflicts. So I'm resubmitting elsewhere.

---
