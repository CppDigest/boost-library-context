# #394 Jacobi Theta functions [Merged]

> Username: evanmiller  
> Created at: 2020-07-07 02:23:46 UTC  
> Updated at: 2020-08-18 10:21:42 UTC  
> Merged at: 2020-08-15 22:51:48 UTC  
> Closed at: 2020-08-15 22:51:48 UTC  
> Url: https://github.com/boostorg/math/pull/394  

Implementations, tests, and ULP plotting programs are provided for the four Jacobi Theta functions per #373. Twenty-four public C++ functions are provided in all, covering various precision-preserving scenarios.  
  
Documentation for collaborators is provided in the code comments. Proper documentation for end users will be provided when the implementation and APIs are finalized.  
  
Some tests are failing; this implementation is meant to start a conversation. The core dilemma faced by the author was that large values of |q| resulted in slow convergence, and sometimes wildly inaccurate results. Following the implementation note in [DLMF 20.14](https://dlmf.nist.gov/20.14), I added code to switch over to the imaginary versions of the theta functions when |q| > 0.85.  This restored accuracy such that all of the identity tests passed for a loose-enough epsilon, but then lost precision to the point that the Wolfram Alpha spot checks failed. It is the author's hope that someone with floating-point experience can tame the exponential dragons and squeeze the ULPs back down to a reasonable range when |q| is large.  
  
When #392 is merged I will add more thorough value tests, although I fully expect them to fail until the underlying precision issues are resolved.  
  
As a final note, the precision issues do not affect the `z=0` case - the ULP plots indicate these return values within 2 ULP across all valid |q|. So that's a start.  
  
Any feedback appreciated.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-07-07 11:09:53 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-654779731  

That looks really good!  
  
You can save me some time by providing a couple of concrete examples which currently have low accuracy?

---

## Comment 2

> Username: evanmiller  
> Created_at: 2020-07-07 11:51:32 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-654802579  

@jzmaddock I can provide specific examples later today but the failing spot tests (`../../../b2 test_jacobi_theta`) should point you in the right direction. Also see the ULP plotting programs added to `reporting/accuracy`. Try large q / small tau (say q = 0.99 or tau = 0.001) with non-zero z (say 1.5) to trigger the `_IMAGINARY` code paths on any of the public functions.  
  
To be clear: the real-valued code paths are inaccurate for large q. The `_IMAGINARY` code paths are accurate but imprecise.   
  
Just so you know: the `_IMAGINARY` functions are rescaled and rearranged a bit - they assume imaginary z, so the sines and cosines became hyperbolic. I then tried to be clever by consolidating the exponentials. Because the identity tests pass I believe the expressions are mathematically correct, but they're losing precision somehow. I suspect either the exp(-z*z) or sqrt(tau) but I am honestly out of my depth here.  
  
Will provide actual examples tonight if I haven't heard from you.

---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-07 13:20:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-443882164  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
  84 |+ // See:
  85 |+ // https://mathworld.wolfram.com/JacobiThetaFunctions.html
  86 |+ // https://dlmf.nist.gov/20
```

> Username: NAThompson  
> Created_at: 2020-07-07 13:20:25 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r450857931  

This documentation is excellent.

> Username: jzmaddock  
> Created_at: 2020-07-07 17:50:02 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451040705  

+1.


---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-07-07 18:23:44 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655041591  

OK I have a test case, thanks.  
  
Minor nits while I think of them:  
  
* test_jacobi_theta.cpp needs to include boost/math/concepts/real_concept.hpp to build.  
* The tests use some constants like M_PI which are Unix'isms: please use boost::math::constants::pi<double>() for portability.  
* There are a bunch of places where you're accidentally calling ::log(double) (for example) rather than std::log(T) which will quite adversely effect the long double precisions.  Adding the using declarations or BOOST_MATH_STD_USING will fix these.  Instantiating the functions with boost::math::concepts::std_real_concept will help find all the locations.  
  
After that the error rates don't look bad (200eps or so), but.... as an example jacobi_theta_2(3, 0.9921875) is so totally trivial to calculate (done in one iteration, and I don't see any obvious things that would loose precision) that I would like to investigate some more.  
  
One possible improvement is in:  
  
  
```  
template <class RealType, class Policy>  
inline RealType  
jacobi_theta2(RealType z, RealType q, const Policy& pol) {  
    if (q <= 0.0 || q >= 1.0) {  
        return policies::raise_domain_error<RealType>("boost::math::jacobi_theta2<%1%>(%1%)",  
                "q must be greater than 0 and less than 1 but got %1%.", q, pol);  
    }  
    return jacobi_theta2tau(z, -log(q)/constants::pi<RealType>(), pol);  
}  
```  
  
where log(q) *might* be better expressed as log1p(q-1) when q is ultra close to 1, but makes no difference in the case above.  And I'm not completely convinced it ever would make a difference, I need to think about that ;)

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-07-07 19:00:43 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655060121  

One more nit: if you instantiate with for example `number<debug_adaptor<cpp_bin_float_50::backend_type>>` ie a type that uses expression templates, then there are quite a few build errors where expressions are passed as function arguments.  
  
The error in the case above come entirely from the line  
  
```  
delta1 = exp(tau*(z*(RealType(2*n) - z/constants::pi<RealType>())  
                -constants::pi<RealType>() * RealType(n)*RealType(n)));  
```  
  
The issue being that z(2n-z/pi) happens to evaluate to very close to pi in this case.  I don't see any algebraic simplification though?

---

## Comment 6

> Username: evanmiller  
> Created_at: 2020-07-07 19:06:15 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655063439  

> * test_jacobi_theta.cpp needs to include boost/math/concepts/real_concept.hpp to build.  
  
Thanks  
  
> * The tests use some constants like M_PI which are Unix'isms: please use boost::math::constants::pi() for portability.  
  
Will do  
  
> * There are a bunch of places where you're accidentally calling ::log(double) (for example) rather than std::log(T) which will quite adversely effect the long double precisions.  Adding the using declarations or BOOST_MATH_STD_USING will fix these.  Instantiating the functions with boost::math::concepts::std_real_concept will help find all the locations.  
  
Aha! I never would have thought of that. It likely affects the use of `fmod` as well - this would basically reduce long doubles to doubles (for the `z` argument) on the _IMAGINARY path. Since my ULP plots use long doubles for the high-precision case, this would basically wreck all the plots.  
  
I'll make these changes and report back.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-07-07 19:07:11 UTC  
> Updated_at: 2020-07-07 19:11:01 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655063945  

@evanmiller : It'll probably a few iterations on this, so make sure to tag your commits with `[CI SKIP]` so we don't DDOS the CI system.  
  
Also, could you drop a performance benchmark into `reporting/performance`? [This one](https://github.com/boostorg/math/blob/develop/reporting/performance/constants_performance.cpp) is a good template, but I'd also template on `float`, `double` and `long double`.

---

## Comment 8

> Username: evanmiller  
> Created_at: 2020-07-07 20:04:46 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655098263  

I've made the changes suggested and added a few more tests covering special values (did you know the third theta function is related to the gamma function?). I don't see any perceptible change to the test results though.  
  
@NAThompson I will look into adding a performance benchmark later tonight.

---

## Comment 9

> Username: evanmiller  
> Created_at: 2020-07-08 01:38:09 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655228608  

Good news! I think I've just about cracked the precision issues. I completed the square on the exponentials, and rewrote them as double-sided summations following [DLMF 20.13.4 and 20.13.5](https://dlmf.nist.gov/20.13).  
  
The mean errors on the MathWorld data are now down to <10 (eps? ULPs? whatever `test_hetero` reports). They were 50-200 before. The ULP plots show the precision getting steadily worse as z grows - so there's still room for improvement. I should have a better picture on the overall accuracy once the 1000-bit code gets plugged in.  
  
BTW What are we benchmarking against exactly? I don't see Jacobi theta functions in either GSL or RMath.  
  
Commit incoming.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2020-07-08 01:53:24 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655232947  

> BTW What are we benchmarking against exactly?   
  
Ourselves! Run the benchmark under `perf record` and examine the asm; you'll enjoy it.

---

## Review 11 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 01:59:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444362081  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
   1 |+ // Jacobi theta functions
   2 |+ // Copyright Evan Miller 2020
```

> Username: NAThompson  
> Created_at: 2020-07-08 01:59:47 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451238955  

Make sure to add the Boost Licence under the copyright statement.

> Username: evanmiller  
> Created_at: 2020-07-08 02:16:55 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451243379  

Will do.


---

## Review 12 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:03:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444363135  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 205 |+         result += delta;
 206 |+         n++;
 207 |+     } while (abs(delta * sqrt(tau)) > eps || (abs(result * sqrt(tau)) > eps * eps && abs(delta/result) > eps));
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:03:24 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451239941  

Could you add a comment about the error analysis that leads to this termination criteria?

> Username: evanmiller  
> Created_at: 2020-07-08 02:12:37 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451242298  

The `sqrt(tau)` stuff was removed in the latest commit, but I can clarify what is left.


---

## Review 13 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:04:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444363467  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 234 |+     } while (abs(delta * sqrt(tau)) > eps || (abs(result * sqrt(tau)) > eps * eps && abs(delta/result) > eps));
 235 |+     
 236 |+     if (abs(result) < eps * eps)
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:04:35 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451240214  

I don't get this one; could you add another comment here?

> Username: evanmiller  
> Created_at: 2020-07-08 02:33:31 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451247479  

I had some tests choking on identities that were supposed to evaluate to zero (e.g. passing in multiples of Pi). I can eliminate this check if you think it's best - it was designed to iron out vanishingly small return values ("epsilon of an epsilon") that the iterator treated as zero anyway.

> Username: jzmaddock  
> Created_at: 2020-07-08 07:27:58 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451337761  

Looks wrong to me as well - very small return values should presumably be possible?  
  
If there's something that should be exactly zero then I'd rather we look at those as special cases.  Also test values that are close to but not exactly zero.

> Username: evanmiller  
> Created_at: 2020-07-08 11:45:42 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451480025  

If I remove this check, I get some disagreement with Wolfram Alpha (which returns 0 instead of a Very Small Number) for e.g. `jacobi_theta4(0, 0.9921875)`. I will leave the check out for now, and hope that the 1000-bit implementation will clear things up.  
  
Regarding exactly zero, this only happens in a couple of cases, as far as I know: `jacobi_theta1(n*pi, q)` and `jacobi_theta2((n+0.5)*pi, q)`. The special case  `jacobi_theta1(0, q)=0` is already handled, but any other case will slip through the floating-point cracks, unless we re-parameterize z to be a phase (à la `sin_pi`).  
  
Studying the equations for `theta3` and `theta4`, these should never truly return zero. Perhaps a strongly worded letter to Wolfram Alpha is in order on this topic.

> Username: NAThompson  
> Created_at: 2020-07-08 13:40:19 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451552097  

Agreement with Wolfram Alpha is not a requirement; there are quite a few bugs in Mathematica. Personally I'd rather see unit tests of properties and quadratures to check accuracy.

> Username: evanmiller  
> Created_at: 2020-07-08 14:26:17 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451587329  

Re: quadrature, the first 3 equations here ought to verify the whole `tau` domain for z=0: https://dlmf.nist.gov/20.10  
  
I previously tried checking relationships with elliptic integrals (e.g. [DLMF 20.9.4](https://dlmf.nist.gov/20.9)), but I ran into some accuracy issues and didn't know which function was to blame. So the existing property checks mostly use simple functions.

> Username: evanmiller  
> Created_at: 2020-07-08 23:45:53 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451885034  

Added quadrature tests and significantly tightened test tolerances in the latest commit. Quadrature is such an elegant way to verify a function, by the way!  
  
Will revisit the elliptic integral relations tonight.

> Username: evanmiller  
> Created_at: 2020-07-09 01:42:42 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451916154  

Added tests against Jacobi elliptic functions (`jacobi_sn`, `jacobi_cd`, etc) in latest commit, as well as a couple of tests against `ellint_rf`.


---

## Review 14 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:05:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444363624  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 251 |+         delta1 = exp(tau*(z*(RealType(2*n) - z/constants::pi<RealType>())
 252 |+                     -constants::pi<RealType>() * RealType(n)*RealType(n)));
 253 |+         // q_n = exp(-tau * constants::pi<RealType>() * RealType(n + 0.5)*RealType(n + 0.5) );
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:05:10 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451240379  

This is commented out in every implementation.

> Username: evanmiller  
> Created_at: 2020-07-08 02:13:14 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451242459  

Removed in latest commit.


---

## Review 15 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:06:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444364166  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 271 |+ _IMAGINARY_jacobi_theta4tau(RealType z, RealType tau, const Policy& pol) {
 272 |+     BOOST_MATH_STD_USING
 273 |+     unsigned n = 1;
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:06:53 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451240854  

I'd go with a 64 bit counter; I'll probably try to get a few million digits out of this and it's best if it doesn't wrap. Also, I'd venture the benchmarks will show there is no performance penalty.

> Username: evanmiller  
> Created_at: 2020-07-08 02:26:23 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451245725  

Well - `n` enters into the equation as `exp(-n*n)`. By the time `n` hits 1000 you'll have roughly 500,000 digits of accuracy.  
  
For machine precision, `n` is lucky to hit 5!


---

## Review 16 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:10:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444365349  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 435 |+         q_n = exp(-tau * constants::pi<RealType>() * RealType(n + 0.5)*RealType(n + 0.5));
 436 |+         delta = q_n * cos(RealType(2*n+1)*z);
 437 |+         result += RealType(2) * delta;
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:10:53 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451241843  

I worry that this cast of `2` to `RealType(2)` will prevent generation of an exponent shift in the exponent and for a mul. I'll see what godbolt says in a bit . .

> Username: evanmiller  
> Created_at: 2020-07-08 02:27:50 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451246075  

Easy enough to change!

> Username: jzmaddock  
> Created_at: 2020-07-08 07:30:08 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451338976  

+1, also in the multiprecision case, multiplying by a literal integer is *much* more efficient than the cast.  BTW we assume in our conceptual requirements that all number types will interact correctly with integer types, so multiplying/dividing by literals (mostly 2!) is pretty commonplace in the library.

> Username: evanmiller  
> Created_at: 2020-07-08 14:37:00 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451595522  

Fixed in latest commit. I opted for `delta + delta` which I imagine compiles to the same thing.


---

## Review 17 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:12:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444365955  

📁 reporting/accuracy/plot_jacobi_theta1.cpp

```diff
   9 |+ int main() {
  10 |+     using PreciseReal = long double; // boost::multiprecision::float128;
  11 |+     using CoarseReal = double;
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:12:57 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451242393  

In this case, there is no problem, but in general I like to use `float128` to test `double`, or use `long double` to test `float`. Obviously that isn't sufficient to guarantee accuracy of the plot, but I have had problems where `long double` wasn't sufficient to get a good ulps plot for `double`.

> Username: evanmiller  
> Created_at: 2020-07-08 02:14:32 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451242757  

`boost::multiprecision::float128` does not compile on my machine - that's why I switched it to `long double`.

> Username: NAThompson  
> Created_at: 2020-07-08 02:20:39 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451244278  

Ok, then I'd use `using CoarseReal = float`; it should only depend on the difference in precisions.


---

## Review 18 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:13:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444366260  

📁 reporting/accuracy/plot_jacobi_theta1.cpp

```diff
  25 |+     plot.clip(clip).width(width);
  26 |+     std::string title = "jacobi_theta1 ULP plot at " + boost::core::demangle(typeid(CoarseReal).name()) + " precision";
  27 |+     //plot.title(title);
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:13:55 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451242608  

Is the title string unused?

> Username: evanmiller  
> Created_at: 2020-07-08 02:15:46 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451243043  

I just copy-pasted this from `test_agm.cpp`, which has the same line commented out.

> Username: NAThompson  
> Created_at: 2020-07-08 02:16:11 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451243137  

Note that if you don't need a title that's fine; but you'll need to label it in the docs.

> Username: NAThompson  
> Created_at: 2020-07-08 02:21:15 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451244417  

Bah then I need to fix that in `test_agm.cpp`. . .

> Username: evanmiller  
> Created_at: 2020-07-08 14:36:00 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451594714  

Uncommented these lines, resolving


---

## Review 19 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 02:17:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444367372  

📁 test/test_jacobi_theta.hpp

```diff
 448 |+             eps);
 449 |+ 
 450 |+     _check_close( // DLMF 20.7.19
```

> Username: NAThompson  
> Created_at: 2020-07-08 02:17:47 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451243597  

If you want, you can use my "math_unit_test.hpp" and use CHECK_ULP_CLOSE if you think it'll be a bit more ergonomic. It also compiles much faster that Boost.Test.


---

## Comment 20

> Username: NAThompson  
> Created_at: 2020-07-08 02:25:44 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655242438  

<img width="1086" alt="Screen Shot 2020-07-07 at 10 23 36 PM" src="https://user-images.githubusercontent.com/5459618/86867301-94aeb780-c0a0-11ea-8f7d-adb8643254c5.png">  
  
It looks like your implementation is now correct; all values are basically in the condition number envelope.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2020-07-08 02:33:07 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655244610  

<img width="1088" alt="Screen Shot 2020-07-07 at 10 32 27 PM" src="https://user-images.githubusercontent.com/5459618/86867956-bfe5d680-c0a1-11ea-8ce9-8c115aeb6153.png">  
  
It's jacobi3 that confuses me. The error is pretty good, but it's uncorrelated with the condition number envelope, which feels very strange.

---

## Review 22 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 12:21:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444708695  

📁 reporting/accuracy/plot_jacobi_theta2.cpp

```diff
  16 |     auto jacobi_theta_precise = [](PreciseReal z) {
  18 |-         return boost::math::jacobi_theta2<PreciseReal>(z, 0.05);
  17 |+         return boost::math::jacobi_theta2<PreciseReal>(z, 0.9);
```

> Username: NAThompson  
> Created_at: 2020-07-08 12:21:10 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451502004  

0.9 is somewhat of a bad choice, since it's parsed as a double and then promoted to long double or parsed as double and cast back to float. That adds a source of inaccuracy that is uninteresting in this context. I'd use a representable here, like 15/16.

> Username: evanmiller  
> Created_at: 2020-07-08 12:51:34 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451519687  

Good catch, will fix.

> Username: evanmiller  
> Created_at: 2020-07-08 23:42:18 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451883968  

Fixed in latest commit.


---

## Review 23 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-08 12:36:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-444719813  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 166 |+ // If the delta is more than an absolute epsilon, keep going.
 167 |+ // If the delta is more than an relative epsilon, and the denominator
 168 |+ // is non-zero, keep going.
```

> Username: NAThompson  
> Created_at: 2020-07-08 12:36:18 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451510555  

I'm not sure this comment is very informative; I was hoping for something like "expected error is cond(f; x)eps/2, so when delta <..." and then some perturbative analysis.

> Username: evanmiller  
> Created_at: 2020-07-08 12:51:10 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451519420  

The comment represents the extent of my thinking and, well, education. I'm happy to incorporate a more formal convergence analysis but it's simply outside my ken.

> Username: NAThompson  
> Created_at: 2020-07-08 13:42:47 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451553821  

lol, alright np. I'll see if I can get a minute to work out some error analysis for these functions.

> Username: jzmaddock  
> Created_at: 2020-07-08 17:00:47 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451693782  

IMO there's no need to require `delta < eps`, normally we would just use `abs(result * eps) > abs(delta)` as if that's true, then adding `delta` can have no effect on `result`.  The only exception would be if `delta` can grow as well as shrink.

> Username: evanmiller  
> Created_at: 2020-07-08 17:21:59 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r451706432  

The sign of `delta` can alternate, which could theoretically put `result` at 0 after two iterations. So I wanted to handle that somehow, and distinguish the situation from an underflow.

> Username: NAThompson  
> Created_at: 2020-08-02 16:14:21 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464095741  

The other point that you are doing a slow division instead of a faster multiplication still stands though. There is still a bit of thinking to do on this termination condition . . .

> Username: NAThompson  
> Created_at: 2020-08-03 13:20:36 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464408322  

Ok, I think I've figured this out. The reason that the sign of `delta` can alternate is because you're including the oscillatory part in it (sin((2n+1)x) or cos((2n+1)x)). I think you should only be testing the magnitude of q^n^2, because the magnitude of complex phase of the original complex valued Jacobi function is always unity. As an aside, the auxiliary functions look much more difficult to implement individually than just using the original Jacobi function in the complex plane. . .

> Username: evanmiller  
> Created_at: 2020-08-03 13:59:49 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464431848  

The convergence tests operate on `q_n` and do not involve the oscillatory part. My original intention was to operate in the complex plane, but the API got too complicated when trying to support arbitrary numeric types.

> Username: NAThompson  
> Created_at: 2020-08-03 14:05:43 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464435526  

Wait then aren't all these calls to `abs` superfluous?

> Username: evanmiller  
> Created_at: 2020-08-03 14:14:49 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464441005  

Sorry, I should have said: the numerator is `q_n` (always positive) but the denominator is `result` (which may oscillate). It would probably be better to change the denominator to `previous_q_n`.

> Username: NAThompson  
> Created_at: 2020-08-03 14:24:16 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464446928  

I personally might track \sum q^{n^2} since it provides a nice L1 bound on the sum.

> Username: evanmiller  
> Created_at: 2020-08-03 15:30:52 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464489919  

I'm not sure the sum will work because of the alternating signs. In addition to the oscillating component there is the (-1)^n in two of the formulas. With q close to 1, the straight sum could be large relative to the final result, and so an error that is small relative to the sum may be large relative to the result.

> Username: evanmiller  
> Created_at: 2020-08-04 18:12:26 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r465237671  

I have updated the convergence criterion to compare `q_n` to the previous one. Everything including ULP plots look pretty much the same. Let me know what you think.


---

## Comment 24

> Username: NAThompson  
> Created_at: 2020-07-09 01:52:35 UTC  
> Updated_at: 2020-07-09 02:03:41 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655848428  

I don't know exactly what you did, but the jacobi_theta3 ulps plot looks a lot better:  
  
<img width="1103" alt="Screen Shot 2020-07-08 at 9 50 04 PM" src="https://user-images.githubusercontent.com/5459618/86987896-1700af80-c165-11ea-8b54-371102a588aa.png">  
  
There is still room for improvement where the condition number envelope -> 0, but now the error is correlated with the condition number.  
  
BTW, jacobi_theta2 looks like I expect it:  
  
<img width="1082" alt="Screen Shot 2020-07-08 at 10 02 17 PM" src="https://user-images.githubusercontent.com/5459618/86988639-b83c3580-c166-11ea-9cee-5f264bd5a635.png">  
  
Other than around q = 1; I expect the error to be much higher there; but it's identically zero. Do you understand this?

---

## Comment 25

> Username: evanmiller  
> Created_at: 2020-07-09 02:17:45 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655855694  

> There is still room for improvement where the condition number envelope -> 0, but now the error is correlated with the condition number.  
  
Yes that does look better - might be the tweak I made to the `m1` functions today.  
> Other than around q = 1; I expect the error to be much higher there; but it's identically zero. Do you understand this?  
  
I am guessing `exp` is underflowing here and these functions are returning 0 at all precisions (cf. Mathematica discussion above).

---

## Comment 26

> Username: evanmiller  
> Created_at: 2020-07-09 02:58:54 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655867348  

The test suite now includes quadrature for the 4 main functions and basically every identity I could get my hands on. I've tightened the tolerances on the existing identity equations to 100EPS or so. Based on the ULP plots, there's room for improvement, but I am basically satisfied with the function implementations at this point. As a bonus, the new tests now serve as a cross-check for related elliptic functions and integrals (including the Riemann zeta function, which I didn't expect to see here!).  
  
When #392 gets merged in, I'll go about generating a grid of high-precision point-test data and finalizing the tolerances. If that all looks good, and assuming everyone is satisfied with the proposed API, then we can start discussing user-facing documentation.  
  
I should warn the audience that I don't have any particular interest in benchmarks or performance optimization with these functions - my preference on that front would be to put it off until later. I do enjoy performance engineering, but I like to know I'm working on the frontier of an actual performance bottleneck before investing any time into it.

---

## Comment 27

> Username: NAThompson  
> Created_at: 2020-07-09 03:03:25 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655868925  

> I should warn the audience that I don't have any particular interest in benchmarks or performance optimization with these functions - my preference on that front would be to put it off until later. I do enjoy performance engineering, but I like to know I'm working on the frontier of an actual performance bottleneck before investing any time into it.  
  
There is no need to agonize, but we at least need to know what the performance is. Part of the fun is learning new skills! Drop a benchmark in real quick and run it under perf; you'll have a blast.

---

## Comment 28

> Username: evanmiller  
> Created_at: 2020-07-09 03:12:22 UTC  
> Updated_at: 2020-07-09 03:13:52 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655871762  

(Following up from @jzmaddock's comment yesterday)  
  
> One possible improvement is in:  
>   
> ```  
> template <class RealType, class Policy>  
> inline RealType  
> jacobi_theta2(RealType z, RealType q, const Policy& pol) {  
>     if (q <= 0.0 || q >= 1.0) {  
>         return policies::raise_domain_error<RealType>("boost::math::jacobi_theta2<%1%>(%1%)",  
>                 "q must be greater than 0 and less than 1 but got %1%.", q, pol);  
>     }  
>     return jacobi_theta2tau(z, -log(q)/constants::pi<RealType>(), pol);  
> }  
> ```  
>   
> where log(q) _might_ be better expressed as log1p(q-1) when q is ultra close to 1, but makes no difference in the case above. And I'm not completely convinced it ever would make a difference, I need to think about that ;)  
  
`log1p` only helps if you have a precise value of `x`. If we `q` is close to 1, then subtracting 1 from it will yield an imprecise result and defeat its purpose. (Think of it this way - if it helped with precision, then `log` would check for values close to 1 and call `log1p` itself.)  
  
It would only make sense to call `log1p` if the user were passing in (for example) the complement of q. But if they have a high-precision complement of q, then the user can always call `log1p` themselves and pass the result (divided by pi) to the `tau` parameterization.

---

## Comment 29

> Username: evanmiller  
> Created_at: 2020-07-09 03:19:58 UTC  
> Updated_at: 2020-07-09 03:28:32 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-655874060  

> There is no need to agonize, but we at least need to know what the performance is. Part of the fun is learning new skills! Drop a benchmark in real quick and run it under perf; you'll have a blast.  
  
My intention was not to antagonize - I'm just trying to prioritize my time.  
  
Edit: Sorry, I misread "agonize" as "antagonize". Time for bed :-)

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2020-07-09 09:14:34 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-656012292  

>log1p only helps if you have a precise value of x  
  
I think that's right.

---

## Comment 31

> Username: evanmiller  
> Created_at: 2020-07-09 19:41:53 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-656315001  

I've added a test data generator to the code base, and put some of its output under test coverage. Right now it's just testing random data in z-tau space - next I'd like to hone in on the problematic area of |q|>0.99.

---

## Comment 32

> Username: evanmiller  
> Created_at: 2020-07-31 20:09:06 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667329414  

I have added user documentation and finalized the test tolerances. A couple of questions:  
  
* Is there a "house style" for equations? I made some equation PNGs/SVGs using a LaTeX wrapper on my machine, and added them to the `equations` folder. I see there is a [script](https://github.com/boostorg/math/blob/develop/doc/equations/generate.sh) to make these files from MML, but the script looks not-especially-portable.  
  
* How do I go about generating a multi-platform Accuracy table for inclusion in the docs?

---

## Comment 33

> Username: NAThompson  
> Created_at: 2020-07-31 20:15:55 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667338351  

@evanmiller : I use [this](https://viereck.ch/latex-to-svg/) to make the equations, then put the result in the `equations` folder. It's not optimal, but it works ok.  
  
> How do I go about generating a multi-platform Accuracy table for inclusion in the docs?  
  
I personally have never bothered with this; if you have an ULPs plot this goes a long way towards understanding the behavior. Also, don't compiler flags have a bigger effect than platform?

---

## Comment 34

> Username: evanmiller  
> Created_at: 2020-08-01 14:03:15 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667536980  

@NAThompson I've added some ULPs plots to the docs. I'm not thrilled with these two:  
  
<img width="779" alt="jacobi_theta1" src="https://user-images.githubusercontent.com/134711/89103049-dcdda300-d3dc-11ea-8cf1-6ae4d3018ee9.png">  
<img width="779" alt="jacobi_theta4" src="https://user-images.githubusercontent.com/134711/89103063-05659d00-d3dd-11ea-8301-3324362e1dfd.png">  
  
But the others look OK. I believe this PR is otherwise ready for final evaluation.

---

## Comment 35

> Username: NAThompson  
> Created_at: 2020-08-01 14:08:15 UTC  
> Updated_at: 2020-08-01 14:09:19 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667537629  

@evanmiller : Those are good! The green line is the condition number of function evaluation; you really can't expect to do better than than unless you arg promote to `long double`.  
  
Locally could you drive up the number of samples and screenshot it? Then we can see how good the q->1 evaluation is.

---

## Comment 36

> Username: evanmiller  
> Created_at: 2020-08-01 14:27:16 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667540214  

OK! I was hoping the errors would center around zero, but if it's good enough for you then it's good enough for me.  
  
Here is a graph zoomed in on q > 0.9 with 25,000 samples:  
  
<img width="779" alt="jacobi_theta1" src="https://user-images.githubusercontent.com/134711/89103643-46f84700-d3e1-11ea-9e29-86017d5706ba.png">  
  
I'm guessing the discontinuities have something to do with the number of iterations / convergence criteria.

---

## Comment 37

> Username: NAThompson  
> Created_at: 2020-08-01 14:47:49 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667542905  

> I was hoping the errors would center around zero  
  
Yeah, that almost never happens; weird structure always arises. There's a section on this in Higham's Accuracy and Stability of Numerical Algorithms, section 1.17 "Rounding Errors are Now Random".

---

## Comment 38

> Username: evanmiller  
> Created_at: 2020-08-01 15:16:03 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667546724  

@NAThompson Thanks. Since the errors are acceptable, I will go ahead and pull off the WIP sticker here. Let me know if I've overlooked anything.

---

## Comment 39

> Username: jzmaddock  
> Created_at: 2020-08-01 17:27:12 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667562948  

>Is there a "house style" for equations? I made some equation PNGs/SVGs using a LaTeX wrapper on my machine, and added them to the equations folder. I see there is a script to make these files from MML, but the script looks not-especially-portable.  
  
Anything broadly similar to what we have already is fine - don't worry about .png's as we don't use them any more (HTML output uses SVG's as does PDF output).  If you could add the latex (if you still have it) as a comment in the .qbk source next to the equation include that would be useful just in case we later spot any typos ;)

---

## Comment 40

> Username: evanmiller  
> Created_at: 2020-08-01 17:32:55 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667563590  

> If you could add the latex (if you still have it) as a comment in the .qbk source next to the equation include that would be useful just in case we later spot any typos ;)  
  
Will do.

---

## Review 41 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:09:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656330  

📁 doc/sf/jacobi_theta.qbk

```diff
  15 |+ [footnote [@https://dlmf.nist.gov/20 Digital Library of Mathematical Functions: Theta Functions, Reinhardt, W. P.,  Walker, P. L.]].
  16 |+ 
  17 |+ Mathematically, the functions are described by:
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:09:07 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464088793  

"Mathematically, the functions are described by:" -> "They are" (no semicolon)

> Username: evanmiller  
> Created_at: 2020-08-02 16:56:10 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464099600  

OK


---

## Review 42 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:09:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656353  

📁 doc/sf/jacobi_theta.qbk

```diff
  18 |+ 
  19 |+ [equation jacobi_theta1] [/ \theta_1(x, q) = 2 \sum_{n=0}^\infty (-1)^n q^{(n+\frac{1}{2})^2} \sin((2n+1)x) ]
  20 |+ [equation jacobi_theta2] [/ \theta_2(x, q) = 2 \sum_{n=0}^\infty q^{(n+\frac{1}{2})^2} \cos((2n+1)x) ]
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:09:21 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464088833  

I personally like `:=` for definitions.

> Username: evanmiller  
> Created_at: 2020-08-02 16:56:19 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464099620  

OK


---

## Review 43 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:10:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656411  

📁 doc/sf/jacobi_theta.qbk

```diff
  22 |+ [equation jacobi_theta4] [/ \theta_4(x, q) = 1 + 2 \sum_{n=1}^\infty (-1)^n q^{n^2} \cos(2nx) ]
  23 |+ 
  24 |+ Graphically, setting /q/=0.15:
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:10:17 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464088932  

I'd put this after the graph, as a caption; then "Plots of the four theta functions for /q/=0.15.

> Username: evanmiller  
> Created_at: 2020-08-02 16:56:52 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464099667  

OK


---

## Review 44 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:12:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656598  

📁 doc/sf/jacobi_theta.qbk

```diff
  35 |+ [equation jacobi_theta_nome] [/ q = \exp(i\pi\tau) ]
  36 |+ 
  37 |+ Note that Boost assumes that [tau] is a purely imaginary number. This assumption is not required by the mathematics, but it does cover the most common application domains.
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:12:53 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464089214  

I like to make every sentence on its own line just to make future diffs more legible.

> Username: evanmiller  
> Created_at: 2020-08-02 16:56:59 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464099674  

OK


---

## Review 45 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:13:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656646  

📁 doc/sf/jacobi_theta.qbk

```diff
  39 |+ [heading Accuracy considerations]
  40 |+ 
  41 |+ The purpose of the [tau] parameterization is to provide increased accuracy either when /q/ is expressible as an exponential or is very close to unity. For example, instead of:
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:13:37 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464089282  

Don't put semicolons before equations; the mathematics is part of the sentence.


---

## Review 46 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:14:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656673  

📁 doc/sf/jacobi_theta.qbk

```diff
  47 |+   jacobi_theta1tau(x, a / boost::math::constants::pi<T>());
  48 |+ 
  49 |+ Internally, Boost implements the /q/ parameterization by taking the logarithm of /q/ and passing it to the [tau] parameterization; as such, using the [tau] parameterization directly will generally yield greater precision. As another example, if the complement of /q/ is known with great accuracy, then instead of:
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:14:04 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464089335  

Newline for each sentence.


---

## Review 47 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:14:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656727  

📁 doc/sf/jacobi_theta.qbk

```diff
  60 |+ 
  61 |+ Results of the theta functions are tested against Wolfram Alpha data, as well as random values computed at high precision. In addition, the tests verify the majority of the identities described in [@https://dlmf.nist.gov/20.7 DLMF Chapter 20.7].
  62 |+ 
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:14:46 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464089400  

Could you include the ulps plots you produced?


---

## Review 48 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:18:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459656976  

📁 doc/sf/jacobi_theta.qbk

```diff
 107 |+ [graph jacobi_theta1_float]
 108 |+ 
 109 |+ The envelope represents the function's [link math_toolkit.cond condition number]. Note that relative accuracy degenerates periodically near [theta][sub 1]=0.
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:18:27 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464089790  

A better link would be:  
  
https://en.wikipedia.org/wiki/Condition_number  
  
(I couldn't get the shard to the one variable section, but that would be even better.


---

## Review 49 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:21:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459657209  

📁 doc/sf/jacobi_theta.qbk

```diff
 105 |+ The following [link math_toolkit.ulps_plots ULPs plot] is representative, fixing /q/=0.5 and varying /x/ from 0 to 2[pi]:
 106 |+ 
 107 |+ [graph jacobi_theta1_float]
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:21:35 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464090143  

Looking at this graph, it appears that the x->0 accuracy is not limited by the condition number of function evaluation and hence it could be implemented better. Is this a case to recommend use of `jacobi_theta1tau`?

> Username: evanmiller  
> Created_at: 2020-08-02 16:41:46 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464098267  

`tau` can help with precision issues on `q` but not `x`.


---

## Review 50 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:23:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459657347  

📁 doc/sf/jacobi_theta.qbk

```diff
 155 |+ [equation jacobi_theta2] [/ \theta_2(x, q) = 2 \sum_{n=0}^\infty q^{(n+\frac{1}{2})^2} \cos((2n+1)x) ]
 156 |+ 
 157 |+ Or in terms of an imaginary [tau]:
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:23:20 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464090321  

I'm confused. You say you enforce that tau is purely imaginary, yet your function signature says `tau` is the same type as `x`. . .

> Username: evanmiller  
> Created_at: 2020-08-02 16:38:36 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464097958  

`tau` the C++ argument is real-typed. The provided number is implicitly multiplied by *i*. This seemed simpler than trying to provide a complex-valued API.


---

## Review 51 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:27:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459657607  

📁 tools/jacobi_theta_data.cpp

```diff
   4 |+ #include <boost/test/included/prg_exec_monitor.hpp>
   5 |+ #include <boost/math/special_functions/jacobi_theta.hpp>
   6 |+ #include <fstream>
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:27:14 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464090748  

I don't understand what this file is doing. It appears you are spitting out values of the function from your own code; but isn't that tautologous?

> Username: evanmiller  
> Created_at: 2020-08-02 16:30:26 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464097166  

This is what the other `tools/*_data.cpp` files do – use a high-precision implementation to compare against the low-precision implementations.

> Username: jzmaddock  
> Created_at: 2020-08-02 17:16:08 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464101422  

Sort of, here's how I approach this:  
  
Let's say we have a principal method, plus a bunch of special case handling (asymptotic expansions at one end of the range say as an example).  The main things that can go wrong:  
  
1) The principal method has an outright bug (eg terms in series not correctly calculated).  
2) The principal method looses precision due to either cancellation or an incorrect termination condition.  
3) There are bugs in the special cases, or the selection logic between them.  
  
We can deal with (1) by comparison to a "known good" - so job done on that I think.  
  
For (2) and (3) what I try and do is calculate spot values at very high precision using the principal method alone, the important thing is to remove all the special case handling as otherwise as @NAThompson says you're not really testing much.  In an ideal world you would use a completely different method, but those are really hard to find.  When I remember, what I should have done (but didn't always) is archive a "naive" version of the principal method in the test value generator file and call that rather than the actual method - I suspect I may have been sloppy and just quickly commented stuff out in the header occasionally though :(  
  
However, with regard to different methods: do the defining series for these functions satisfy that?  They don't have to be fast for test value generation as they're only run the once.  In extreme cases (1F1) I've resorted to interval arithmetic using mpfi_float to handle series with lots of cancellation when generating spot tests.

> Username: evanmiller  
> Created_at: 2020-08-02 18:21:06 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464107720  

That makes sense. We have essentially two implementations of each function, the `IMAGINARY` path (for `tau` < 1) and the regular path. The code chooses the one that is expected to converge more quickly. So one possibility would be to invert the branch logic so that the test data always chooses the slower path when generating test data.


---

## Comment 52

> Username: NAThompson  
> Created_at: 2020-08-02 15:53:20 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667691049  

@evanmiller : Could you drop this file into `reporting/performance/jacobi_theta_performance.cpp`?  
  
```cpp  
//  (C) Copyright Nick Thompson 2020.  
//  Use, modification and distribution are subject to the  
//  Boost Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#include <random>  
#include <benchmark/benchmark.h>  
#include <boost/math/special_functions/jacobi_theta.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using boost::multiprecision::number;  
using boost::multiprecision::mpfr_float_backend;  
using boost::multiprecision::float128;  
using boost::multiprecision::cpp_bin_float_50;  
using boost::multiprecision::cpp_bin_float_100;  
using boost::math::jacobi_theta1;  
using boost::math::jacobi_theta1tau;  
  
template<class Real>  
void JacobiTheta1(benchmark::State& state)  
{  
    std::random_device rd;  
    std::mt19937_64 mt(rd());  
    std::uniform_real_distribution<long double> unif(0,0.01);  
  
    Real x = static_cast<Real>(unif(mt));  
    Real q = static_cast<Real>(unif(mt));  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(jacobi_theta1(x, q));  
        x += std::numeric_limits<Real>::epsilon();  
    }  
}  
  
BENCHMARK_TEMPLATE(JacobiTheta1, float);  
BENCHMARK_TEMPLATE(JacobiTheta1, double);  
BENCHMARK_TEMPLATE(JacobiTheta1, long double);  
BENCHMARK_TEMPLATE(JacobiTheta1, float128);  
BENCHMARK_TEMPLATE(JacobiTheta1, number<mpfr_float_backend<100>>);  
BENCHMARK_TEMPLATE(JacobiTheta1, number<mpfr_float_backend<200>>);  
BENCHMARK_TEMPLATE(JacobiTheta1, number<mpfr_float_backend<300>>);  
BENCHMARK_TEMPLATE(JacobiTheta1, number<mpfr_float_backend<400>>);  
BENCHMARK_TEMPLATE(JacobiTheta1, number<mpfr_float_backend<1000>>);  
BENCHMARK_TEMPLATE(JacobiTheta1, cpp_bin_float_50);  
BENCHMARK_TEMPLATE(JacobiTheta1, cpp_bin_float_100);  
  
template<class Real>  
void JacobiTheta1Tau(benchmark::State& state)  
{  
    std::random_device rd;  
    std::mt19937_64 mt(rd());  
    std::uniform_real_distribution<long double> unif(0,0.01);  
  
    Real x = static_cast<Real>(unif(mt));  
    Real q = static_cast<Real>(unif(mt));  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(jacobi_theta1tau(x, q));  
        x += std::numeric_limits<Real>::epsilon();  
    }  
}  
  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, float);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, double);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, long double);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, float128);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, number<mpfr_float_backend<100>>);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, number<mpfr_float_backend<200>>);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, number<mpfr_float_backend<300>>);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, number<mpfr_float_backend<400>>);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, number<mpfr_float_backend<1000>>);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, cpp_bin_float_50);  
BENCHMARK_TEMPLATE(JacobiTheta1Tau, cpp_bin_float_100);  
  
BENCHMARK_MAIN();  
```  
  
Also, running this file, I find that something has gone wrong with the ADL for `mpfr` types, there are some warnings from unused policies from `g++-10`.

---

## Review 53 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 15:55:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459659670  

📁 doc/sf/jacobi_theta.qbk

```diff
  32 | 
  33 | Most applications will want to use the /q/ parameterization of the functions: `__jacobi_theta1`, `__jacobi_theta2`, `__jacobi_theta3`, and `__jacobi_theta4`, where /q/ is restricted to the domain (0, 1). However, a second [tau] parameterization is provided for all four functions, where
```

> Username: NAThompson  
> Created_at: 2020-08-02 15:55:20 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464093861  

Is there equivalent Mathematica syntax that has the same nome/tau/scaling conventions? It might be nice to put that in here  . . .  
  
I see [EllipticTheta[a,q,z]](https://reference.wolfram.com/language/ref/EllipticTheta.html)

> Username: evanmiller  
> Created_at: 2020-08-02 16:36:57 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464097827  

I don't think Mathematica provides a tau parameterization. I'll add a link in the docs to EllipticTheta, though.

> Username: NAThompson  
> Created_at: 2020-08-02 17:06:08 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464100503  

I'm getting more confused about the `tau` parametrization. It now looks to me like you are trading a multiplication on each iteration for a logarithm and a call to `std::exp` on each iteration.  . .

> Username: evanmiller  
> Created_at: 2020-08-02 17:27:37 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464102471  

Implementing purely in `q`, without a `log`, would require a `pow` in place of the `exp`. The private `IMAGINARY` code paths have to do the `exp` anyway since they are implementing hyperbolic sine and cosine.  
  
Exposing `tau` has the added benefit of letting the user provide a pre-logged argument for the sake of precision, e.g. with `q` close to 1 (so the user can use `log1p`) or if the argument provided would have been an exponential such as `exp(-a)`.

> Username: NAThompson  
> Created_at: 2020-08-02 17:35:35 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464103263  

You don't need a call to `pow`, since q^(n+1)^2 = q^n^2*qn*qn*q. Here's some pseudocode:  
  
```cpp  
template<typename Real>  
Real new_theta1(Real x, Real q)  
{  
    using std::sqrt;  
    using std::sin;  
    using std::abs;  
    Real scale = 2*sqrt(sqrt(q));  
    Real term = sin(x);  
    Real sum = term;  
    size_t n = 1;  
    Real qn = q;  
    Real qnsq = q;  
    Real prefactor = qnsq*qn;  
    while (prefactor > abs(sum)*std::numeric_limits<Real>::epsilon())  
    {  
        term = prefactor*sin((2*n+1)*x);  
        if (n & 1) {  
            sum -= term;  
        } else {  
            sum += term;  
        }  
        qn *= q;  
        qnsq = qnsq*q*qn*qn;  
        prefactor = qnsq*qn;  
        ++n;  
    }  
    return scale*sum;  
}  
```  
  
This executes in 13ns on my machine. More improvements are available from a Clenshaw recurrence.

> Username: evanmiller  
> Created_at: 2020-08-02 19:25:00 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464114175  

Very cool! Happy to explore performance improvements after everything else is settled.


---

## Review 54 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 16:11:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459660667  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 434 |+ 
 435 |+     if (abs(result) < eps * eps)
 436 |+         return RealType(0);
```

> Username: NAThompson  
> Created_at: 2020-08-02 16:11:01 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464095430  

I don't understand why this needs to be done. There are plenty of floating point numbers less than eps*eps; they are perfectly valid outputs aren't they?

> Username: evanmiller  
> Created_at: 2020-08-02 16:26:51 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464096832  

You are correct; I removed similar checks after previous discussion, but forgot to take this one out as well.


---

## Comment 55

> Username: evanmiller  
> Created_at: 2020-08-02 16:30:51 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-667695566  

> @evanmiller : Could you drop this file into `reporting/performance/jacobi_theta_performance.cpp`?  
  
Will do!

---

## Review 56 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 16:58:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459663601  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 187 |+ 
 188 |+     do {
 189 |+         delta = exp(-tau*z_n*z_n/constants::pi<RealType>());
```

> Username: NAThompson  
> Created_at: 2020-08-02 16:58:45 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464099821  

Maybe precompute `tau/constants::pi<RealType>()` so that there isn't a division on each iteration.  
  
People don't realize that elementary special function evaluations are in the same complexity class as division!

> Username: evanmiller  
> Created_at: 2020-08-02 17:39:06 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464103542  

If we're going to squeeze out divisions like that, it might make sense to divide `tau` by `pi` further up the call stack.


---

## Review 57 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 16:59:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459663654  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 314 |+     do {
 315 |+         q_n = exp(-tau * constants::pi<RealType>() * RealType(n + 0.5)*RealType(n + 0.5) );
 316 |+         delta = q_n * sin(RealType(2*n+1)*z);
```

> Username: NAThompson  
> Created_at: 2020-08-02 16:59:34 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464099890  

Calling `sin` on each iteration seems expensive; can't it be done via a Clenshaw recurrence?

> Username: evanmiller  
> Created_at: 2020-08-02 17:34:45 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464103162  

The iteration count is typically between 2 and 5 so I'm not sure a recurrence formula will buy much (also because there's already an `exp` in each iteration).

> Username: NAThompson  
> Created_at: 2020-08-02 17:48:13 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464104396  

Ok, so most Clenshaw recurrence discussions descend rather than ascend, so we instead can use  
  
sin((2(n+1)+1)x) = sin((2n+1)x)*cos(2x) + cos((2n+1)x)*sin(2x)

> Username: NAThompson  
> Created_at: 2020-08-02 18:04:31 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464105938  

Combine this with the recurrence for q^n^2 and you can do the whole thing with a single call to std::cos and std::sin and then only muls and adds after that.


---

## Review 58 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-02 17:00:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459663702  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 378 |+     do {
 379 |+         q_n = exp(-tau * constants::pi<RealType>() * RealType(n + 0.5)*RealType(n + 0.5));
 380 |+         delta = q_n * cos(RealType(2*n+1)*z);
```

> Username: NAThompson  
> Created_at: 2020-08-02 17:00:22 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464099981  

Clenshaw recurrence instead of `cos` on each iteration?


---

## Review 59 [Commented]

> Username: jzmaddock  
> Created_at: 2020-08-02 18:31:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-459669377  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 286 |+ template <class RealType, class Policy>
 287 |+ inline RealType
 288 |+ jacobi_theta1tau(RealType z, RealType tau, const Policy& pol)
```

> Username: jzmaddock  
> Created_at: 2020-08-02 18:31:35 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464108960  

Point of pedantry: I'd prefer it if the actual entry points followed the logic in https://www.boost.org/doc/libs/1_73_0/libs/math/doc/html/math_toolkit/special_tut/special_tut_impl.html, what we gain is the ability to handle arguments of different types (including integers), plus expression templates when used with multiprecision types.  
  
On a related note, I'd like to see the entry points added to this test: https://github.com/boostorg/math/blob/develop/test/compile_test/instantiate.hpp,     
  
Also note that each special function appears in multiple sections in that file - you will very likely get test failures when doing this, but it does catch a lot of issues - including now, not forwarding policies correctly ;)

> Username: evanmiller  
> Created_at: 2020-08-02 19:16:57 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464113399  

To be clear - should I be using `promote_args` and friends in the public API? (I was advised against this previously.)  
  
I've added the entry points (privately) to instantiate.hpp - lots of failures - will keep me busy tomorrow.

> Username: evanmiller  
> Created_at: 2020-08-03 05:20:13 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464199159  

How's it look now?

> Username: jzmaddock  
> Created_at: 2020-08-03 08:05:01 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464255845  

>To be clear - should I be using promote_args and friends in the public API? (I was advised against this previously.)  
  
Yeah, my bad, you had a complex number interface before and promote_args doesn't work (easily) with that.  Now that it's all real-valued, might as well be consistent with everything else.  
  
aside 1: promote_args is mis-named, should really be "make_common_floating_point_type" or something.  
aside 2: I guess it would help if I updated it to function with complex number types as well!  
  
>I've added the entry points (privately) to instantiate.hpp - lots of failures - will keep me busy tomorrow.  
  
Likely most will disappear once the argument type handling is in place.  
  
>How's it look now?  
  
You're way ahead of me, will catch up with this later!


---

## Review 60 [Commented]

> Username: jzmaddock  
> Created_at: 2020-08-03 19:03:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/394#pullrequestreview-460270681  

📁 include/boost/math/special_functions/jacobi_theta.hpp

```diff
 178 |+ inline bool
 179 |+ _jacobi_theta_converged(RealType result, RealType delta, RealType eps) {
 180 |+     return abs(delta) < eps && (abs(result) == 0.0 || abs(delta/result) < eps);
```

> Username: jzmaddock  
> Created_at: 2020-08-03 19:03:23 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464605758  

You need a using std::abs here.

> Username: evanmiller  
> Created_at: 2020-08-03 19:49:15 UTC  
> Updated_at: 2020-08-04 18:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#discussion_r464626785  

I will plan to eliminate `abs` when I revamp this function per other discussions.


---

## Comment 61

> Username: jzmaddock  
> Created_at: 2020-08-15 19:06:21 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-674436520  

This looks like it's ready to go?  @NAThompson ?

---

## Comment 62

> Username: NAThompson  
> Created_at: 2020-08-15 19:44:57 UTC  
> Updated_at: 2020-08-15 19:45:11 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-674440077  

I'm happy with this; the only potential point of improvement is using the trigonometric recurrence and the q^{n^2} recurrence to increase the speed. But perfect is the enemy of the good.

---

## Comment 63

> Username: evanmiller  
> Created_at: 2020-08-15 20:30:06 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-674444222  

@NAThompson's ideas may also yield accuracy improvements – but those can always take the form of a future PR. So you have my blessing to merge as well.

---

## Comment 64

> Username: pulver  
> Created_at: 2020-08-17 23:50:43 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-675171193  

Are the build errors on https://travis-ci.org/github/boostorg/math/builds/718417184 related to this PR?

---

## Comment 65

> Username: NAThompson  
> Created_at: 2020-08-18 00:13:36 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-675177336  

My bad; I told @evanmiller to `[CI SKIP]` until he was ready to merge, and then I just merged it. So looks like this one is my fault.

---

## Comment 66

> Username: jzmaddock  
> Created_at: 2020-08-18 07:32:27 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-675309985  

Testing the "obvious" fixes now....

---

## Comment 67

> Username: jzmaddock  
> Created_at: 2020-08-18 10:21:42 UTC  
> Url: https://github.com/boostorg/math/pull/394#issuecomment-675395261  

Tentative fixes pushed: just looks like the usual concept-failures.  Glad someone was paying attention to develop results as I missed that!

---
