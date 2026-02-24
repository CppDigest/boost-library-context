# #421 - "Kolmogorov-Smirnov" distribution [Closed]

> Username: evanmiller  
> Created at: 2020-08-18 18:18:39 UTC  
> Updated at: 2020-09-04 14:35:09 UTC  
> Closed at: 2020-09-04 14:35:09 UTC  
> Url: https://github.com/boostorg/math/issues/421  

I'm interested in contributing code needed to implement a [Kolmogorov-Smirnov test](https://en.wikipedia.org/wiki/Kolmogorov–Smirnov_test). The distribution under the null is unofficially called a "Kolmogorov-Smirnov distribution" for lack of anything better.  
  
This paper has a good overview of the various algorithms implementing the distribution in asymptotic and exact forms:  
  
https://www.jstatsoft.org/article/view/v039i11  
  
The exact form is (IMO) computationally prohibitive, requiring either FFT or eigendecomposition.  
  
The asymptotic form comes in a couple of major flavors, Kolmogorov's original 1st order Taylor approximation or a 4th order approximation from Pelz and Good:  
  
https://www.jstor.org/stable/2985019  
  
I'm curious how I should handle the various orders of this equation. What I'd like to do is implement the 1st order equation now (this is fairly popular in stats software), but also have some flexibility to add higher order equations later. I'd also like to leave room in the API for an implementation of the exact distribution if someone smarter than me figures out a good way to support it.  
  
So my question is one of API design. Would I call it one of:  
  
* `kolmogorov_dist`  
* `kolmogorov_smirnov_dist`  
* `kolmogorov_asymptotic_dist`  
* `kolmogorov_smirnov_asymptotic_dist`  
  
And in the future, how should I handle the order of the Taylor expansion? Have an integer parameter specifying the order, defaulting to 1? Is there any precedent in any of the other distributions or special functions for specifying Taylor expansion order?

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-08-18 18:32:29 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675643804  

> And in the future, how should I handle the order of the Taylor expansion? Have an integer parameter specifying the order, defaulting to 1? Is there any precedent in any of the other distributions or special functions for specifying Taylor expansion order?  
  
The closest analogy is specifying smoothness via an integer. For example, the number of vanishing moments of a Daubechies wavelet is specified via an integer template parameter. See [here](https://github.com/boostorg/math/blob/b1c03c841ad8f2fcae7af1708d263d85cf7cefe9/include/boost/math/special_functions/daubechies_wavelet.hpp#L70); another is the cardinal B-splines which support width is proportional to smoothness; see [here](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/cardinal_b_spline.hpp#L33).  
  
Note that I don't have a good solution here; an integer template parameter seems sensible but might not be appropriate.

---

## Comment 2

> Username: evanmiller  
> Created at: 2020-08-18 18:55:20 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675654453  

I was thinking of a parameter to the distribution itself in addition to the degrees of freedom, e.g.  
  
```  
boost::math::kolmogorov_asymptotic_distribution<>(10) // N=10, order=1  
boost::math::kolmogorov_asymptotic_distribution<>(10, 1) // N=10, order=1  
boost::math::kolmogorov_asymptotic_distribution<>(10, 4) // N=10, order=4  
```

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-08-18 18:59:48 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675656569  

That looks good to me; @pabristow knows the distributions the best though.

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-08-18 19:53:17 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675680468  

@evanmiller : Did you see we already have the Anderson-Darling test?

---

## Comment 5

> Username: evanmiller  
> Created at: 2020-08-18 20:02:55 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675684758  

@NAThompson Hadn't seen it, good to know! A full K-S test would be easy to add to that folder once the limiting distribution is in place (the test statistic *D* is just the max. difference between two CDFs).

---

## Comment 6

> Username: ckormanyos  
> Created at: 2020-08-18 20:33:22 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675698338  

> requiring either FFT  
  
If you decide to go further in this, do you know how might handle FFT implementations?  
I think we still do not have plans nor solid plans for FFT, although I think some rudimentary support for FFTs would be super cool.

---

## Comment 7

> Username: evanmiller  
> Created at: 2020-08-18 20:42:36 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675710035  

@ckormanyos I don't know much about the FFT ecosystem... See this paper for the theory and implementation of an exact KS test in R:  
  
https://openaccess.city.ac.uk/id/eprint/18541/8/Dimitrova%2C%20Kaishev%2C%20Tan%20%282017%29%20KSr1.pdf  
  
Their library requires FFTW3.  
  
I *think* an eigendecomposition could do the job for the continuous case. The Durbin formula requires computing H^n, which is easy if H is diagonalizable. But we'd need LAPACK or similar I believe.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2020-08-19 05:40:04 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-675863212  

> Their library requires FFTW3.  
  
I agree with that, as meeting or beating FFTW would be a tall task indeed.  
  
You may wonder why I asked. It is because somewhere in the future, I might want or expect some of Multiprecision to also need an FFT. And it would at least be a good idea (when requiring a 3rd party FFT package) for Math and Multiprecision to tend to use the same one.  
  
So if we could agree on, well this stuff in Boost runs but needs FFT whatever and it's both FFTW, then that would make sense to me such that the number of 3rd party libs is reduced to one.

---

## Comment 9

> Username: pabristow  
> Created at: 2020-08-19 12:06:41 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-676239860  

Hmmm.  Looks less than simple - and likely above my pay grade :-(   
  
The Richard Simard, Pierre L'Ecuyer algorithm looks almost heroic in the number of regions handles separately.    
  
I note with concern that it is not even agree that the K-S test is best.   
  
It's clearly good if we can agree to provide a Boost wrapper around FFTW as they seem to be the market-leader.  
  
Using multiprecision can stave off cancellation error - but not for long, and at a big time cost.  
  
@jzmaddock has performed magic with approximations (as I discovered to my cost implementing the Lambert W function when his method proved more accurate and faster).  I'm sure he will comment.  
  
In general, I suspect template parameters cost compile time, but save compute time? And have been widely used in Boost.Math.  
  
Finally I'm sure I'm at least qualified to express an opinion on name ;-)  
  
Kolmogorov_distribution  seems optimal to me.  Everyone has the duo twinned mentally.  And the test always mentions both.  But it seems that only Kolmogorov did the distribution? We have not abbreviated distribution to dist in other distributions.

---

## Comment 10

> Username: NAThompson  
> Created at: 2020-08-19 12:46:48 UTC  
> Updated at: 2020-08-19 12:52:06 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-676295887  

@evanmiller : We do have precedent for requiring fftw as a dependency in this library. See [trigonometric interpolation](https://github.com/boostorg/math/blob/develop/include/boost/math/interpolators/detail/cardinal_trigonometric_detail.hpp) and the [Chebyshev transform](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/chebyshev_transform.hpp).  
  
> The exact form is (IMO) computationally prohibitive, requiring either FFT or eigendecomposition.  
  
I can see that eigendecomposition might be too expensive, but why is an FFT? (Sorry haven't read the linked paper yet.)

---

## Comment 11

> Username: evanmiller  
> Created at: 2020-08-19 13:02:11 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-676318779  

> I can see that eigendecomposition might be too expensive, but why is an FFT? (Sorry haven't read the linked paper yet.)  
  
FFT might be doable – I haven't delved into the details, either.

---

## Comment 12

> Username: evanmiller  
> Created at: 2020-08-19 13:08:07 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-676327674  

Thanks for the comments everyone. I have enough code on this end to get a PR going, which I will open shortly.  
  
In the interest of clarity I have called it `kolmogorov_smirnov_distribution`. Smirnov may have been a lowly tabulator, but Kolmogorov was so prolific that additional identifiers always help. I'm implementing just the 1st order asymptote to start, with the idea that an order parameter (or exact version) can be added at a later date.  
  
We can continue the conversation in more depth once there is some code to chew on. Hang tight...

---

## Comment 13

> Username: NAThompson  
> Created at: 2020-08-19 13:39:32 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-676374745  

@evanmiller : Have you seen [this paper](https://core.ac.uk/download/pdf/25787785.pdf)? It looks like they did pretty well there.

---

## Comment 14

> Username: pabristow  
> Created at: 2020-08-19 13:44:22 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-676381916  

https://arxiv.org/pdf/1802.06966.pdf  also looks more promising by rewriting equations to avoid cancellation error.

---

## Comment 15

> Username: evanmiller  
> Created at: 2020-08-19 13:51:45 UTC  
> Url: https://github.com/boostorg/math/issues/421#issuecomment-676393196  

@NAThompson I'm not happy with the MTW paper as they take large powers of large matrices. I suspect it could be improved with eigendecomposition. H^n = Q * λ^n * Q^-1. Then head off the numerical overflow by inserting Durbin's n! / n^n term into the diagonal eigenvalue matrix.  
  
@pabristow Hadn't seen that paper, will take a look!  
  
@ Everybody I've opened a PR so you can see what I have so far.
