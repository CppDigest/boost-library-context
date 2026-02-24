# #1244 feat: Extend hypergeometric distribution PMF for non-integral arguments [Open]

> Username: fpelliccioni  
> Created at: 2025-02-04 19:35:17 UTC  
> Updated at: 2026-02-13 23:42:34 UTC  
> Url: https://github.com/boostorg/math/pull/1244  

# Summary  
  
This PR extends the hypergeometric distribution's PMF to support non-integer values of `x` using cubic Hermite interpolation. If `x` is not an integer, the implementation selects at least three valid integer points for interpolation. If fewer than three points are available, it raises a domain error.  
  
# Known Issue  
  
This change introduces a dependency on `cubic_hermite`, which throws exceptions instead of using Boost.Math’s `policies::raise_domain_error`. As a result, some compile-time tests fail due to exceptions being disabled in those tests.  
  
# Next Steps  
  
- A separate PR will update `cubic_hermite` (and potentially other interpolators) to conform to Boost.Math’s policy-based error handling.  
- This PR is left in draft so reviewers can provide feedback on the approach.  
  
# Question: Best way to test this?  
  
Hypergeometric test data in `test/hypergeometric_test_data.ipp` appears to be auto-generated from Mathematica, with some values removed due to absolute vs relative error concerns.  
Since we now support non-integer values, what is the best approach to generate a new test dataset? Does anyone know of a reliable source for non-integer hypergeometric PMF values?  
  
Fixes https://github.com/boostorg/math/issues/1240

---

## Comment 1

> Username: mdhaber  
> Created_at: 2025-02-14 07:36:41 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-2658494655  

Thanks for this @fpelliccioni!  
  
@steppi would interpolation be ok, or were you hoping for the definition to match that of evaluation in terms of `binom`?  
  
@fpelliccioni it might solve some of the testing and policy issues to implement this using binomial coefficients for small arguments and (the existing?) Lanczos approximation for large arguments; e.g. see https://github.com/scipy/scipy/pull/22312#discussion_r1932918404. Then reference data could be computed in Mathematica using `Binomial`.

---

## Comment 2

> Username: steppi  
> Created_at: 2025-02-14 14:42:23 UTC  
> Updated_at: 2025-02-14 14:42:37 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-2659510817  

> @steppi would interpolation be ok, or were you hoping for the definition to match that of evaluation in terms of `binom`?  
  
Yeah, I was hoping this would evaluate  
  
$$\frac{{r \choose k}{N -r \choose k}}{{N \choose n}}$$  
  
for non-integral arguments. Interpolating with Hermite polynomials would actually work for our application in stats though, but is kind of arbitrary, and thus like you and @fpelliccioni said, there would be no natural reference implementation to compare to. I'm pretty sure the Lanczos code path in Boost's hypergeometric pmf implementation will just work out of the box for non-integral arguments.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2025-02-14 16:46:49 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-2659809708  

> Since we now support non-integer values, what is the best approach to generate a new test dataset? Does anyone know of a reliable source for non-integer hypergeometric PMF values?  
  
Could you do a quadrature over the entire domain and assert its close to unity? That would at least catch gross errors, or demonstrate one way or another whether the particular interpolator chosen is somehow "respecting" the structure of the PMF.  
  
Another question: You are using `cubic_hermite` so it appears you know how to recover derivatives of this at integer arguments. Can you recover multiple derivatives and use `quintic_hermite`?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-02-14 18:19:12 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-2659987751  

Before we get too carried away with interpolation, why not just use the binomial definition (and our existing code) over the real domain?  That would seem to be more accurate, and probably rather more efficient as well.  
  
A more pressing (and perhaps difficult) question is what we **want** to have happen?  Is there any precedence for evaluating the hypergeometric over a non-discrete domain?  Or to put this another way: is anyone relying on the hypergeometric PMF generating an error when a non-integer is passed?

---

## Comment 5

> Username: mdhaber  
> Created_at: 2025-02-14 18:26:46 UTC  
> Updated_at: 2025-02-14 18:27:10 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-2660003154  

@jzmaddock This request ultimately stems from https://github.com/scipy/scipy/pull/22312#discussion_r1912619742. We asked to be able to evaluate the function at non-integer arguments (like some other Boost discrete disributions) so that we can approximate infinite sums involving the PMF in a monotonically decreasing summand with an integral. Any shape-preserving interpolation would work (so PCHIP might be preferred to avoid ringing), but the original suggestion (repeated here, https://github.com/boostorg/math/pull/1244#issuecomment-2658494655) was indeed to use the binomial definition and the existing code where possible.

---

## Comment 6

> Username: JacobHass8  
> Created_at: 2026-01-08 20:02:23 UTC  
> Updated_at: 2026-01-08 20:05:59 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-3725536173  

> Before we get too carried away with interpolation, why not just use the binomial definition (and our existing code) over the real domain? That would seem to be more accurate, and probably rather more efficient as well.  
  
I've implemented the Lanczos code path and Gamma function code path as mentioned in https://github.com/boostorg/math/pull/1244#issuecomment-2659510817. However, the accuracy of the results aren't impressive for doubles. I've tried $N=170$ and $N=500$, and was only able to get about 10 significant digits to match Mathematica.   
  
I can make a new PR with this implementation, but it may require an overhaul depending on how precise the results are expected to be.

---

## Comment 7

> Username: mdhaber  
> Created_at: 2026-02-13 07:02:30 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-3895256205  

> I can make a new PR with this implementation, but it may require an overhaul depending on how precise the results are expected to be.  
  
@JacobHass8 Others may have different requirements, but any monotonic interpolation between non-integral arguments would satisfy the original request. Thanks for trying this!

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2026-02-13 13:32:29 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-3897273881  

>I've implemented the Lanczos code path and Gamma function code path as mentioned in https://github.com/boostorg/math/pull/1244#issuecomment-2659510817. However, the accuracy of the results aren't impressive for doubles. I've tried N = 170 and N = 500 , and was only able to get about 10 significant digits to match Mathematica.  
  
I'm not surprised, evaluating via logs will always lead to terrible cancellation errors, and there's just no real way to avoid this since ultimately we're trying to evaluate the ratio of two humongously large numbers.  It's probably about as good as we can do in this case though (unless `N < max_factorial<T>()` in which case we can avoid logs altogether).  
  
With regard to interface, I feel a separate free function is probably the way to go, rationale:  
  
1) The hypergeometric distribution is very much discreet, and we should probably retain that.  
2) There is no obvious means to evaluate the CDF in the continuous case (nor any real need?)  
  
thoughts?

---

## Comment 9

> Username: mdhaber  
> Created_at: 2026-02-13 16:41:42 UTC  
> Updated_at: 2026-02-13 23:42:34 UTC  
> Url: https://github.com/boostorg/math/pull/1244#issuecomment-3898172918  

~~I think there is precedent for Boost's discrete distributions to have distinct, non-zero PMF at (non-integral) `x` and `floor(x)` while the CDF produces the same result when evaluated at `x` and `floor(x)`. I can't write an example with Boost directly, but we rely on Boost for the binomial and negative binomial distributions in SciPy, the wrapped functions work that way, and I don't think we're adding interpolation. So I'd have a mild preference for that. That said, if the continuous version of the function were to produce exactly the same values as the discrete version at integral points, we could simply swap in the continuous version; our code makes sure the *public* version of the PMF is zero at non-integral points.~~  
  
I was mistaken. Looks like Boost binomial and negative binomial CDFs also produce distinct results at non-integral `x` and `floor(x)`.   
  
It looks like the hypergeometric CDF can be expressed in terms of a generalized hypergeometric function, so it has a natural continuous extension. Actually, that would be useful for our generic implementation of the quantile function. (Even if Boost provides a `ppf`, we still expose both that and the generic inversion method to the user, and we test the two against one another to ensure that distributions are self-consistent.)

---
