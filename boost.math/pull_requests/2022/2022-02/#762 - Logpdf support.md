# #762 Logpdf support [Merged]

> Username: mborland  
> Created at: 2022-02-15 17:00:02 UTC  
> Updated at: 2022-04-21 18:48:02 UTC  
> Merged at: 2022-04-19 01:45:50 UTC  
> Closed at: 2022-04-19 01:45:51 UTC  
> Url: https://github.com/boostorg/math/pull/762  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-15 18:28:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-883451786  

📁 include/boost/math/distributions/arcsine.hpp

```diff
 391 |+       using boost::math::constants::pi;
 392 |+       using boost::math::constants::half;
 393 |+       result = -half<RealType>() * log(-(x - 1) * x) - log(pi<RealType>());
```

> Username: jzmaddock  
> Created_at: 2022-02-15 18:28:44 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r807169602  

That assumes the support is `[0,1]` but is actually `[this->lo, this->hi]`

> Username: mborland  
> Created_at: 2022-02-15 18:32:14 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r807172513  

I realized I messed that up when I added the tests. Fixed in latest commit.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-02-15 18:38:41 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1040637199  

Thanks Matt, that's more or less along the lines I was thinking too.  
  
I think we can safely add a default implementation:  
  
```  
template <class Distribution>  
inline typename Distribution::value_type logpdf(const Distribution& dist, const typename Distribution::value_type& x)  
{  
  using std::log;  
   return log(pdf(dist, x));  
}  
```  
  
For those distributions that do not have exponential-like pdf's.  The Arcsine is actually a good case in point, do we need an actual implementation here, or is the default good enough?  I can see some arguments either way: if `this->hi` is super large then (x-lo)(hi-x) could overflow (but no one has ever complained), on the other hand, evaluating by logs as you do, may lead to cancellation error when the pdf is close to 1.  And I doubt there is much performance difference between them.  
  
With regard to testing... we already have decent tests for the pdf's, so I suspect that a basic sanity check against the pdf may be good enough in thee cases?

---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-24 15:56:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-892645156  

📁 test/test_normal.cpp

```diff
 231 |    BOOST_CHECK_CLOSE(
 232 |       logpdf(normal_distribution<RealType>(), static_cast<RealType>(0)),
 233 |       log(static_cast<RealType>(0.3989422804014326779399460599343818684759L)), // 1/sqrt(2*pi)
```

> Username: NAThompson  
> Created_at: 2022-02-24 15:56:33 UTC  
> Updated_at: 2022-02-24 15:56:34 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r814024394  

Isn't 1/sqrt(2pi) in boost::math::constants?


---

## Comment 4

> Username: mborland  
> Created_at: 2022-02-25 08:34:02 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1050643805  

@jzmaddock This is clean and good for review. Logpdf has been specialized when there are exponentials in the pdf and there rest will default to the naive `log(pdf)`. The only place where I had to adjust tolerances was in the test of long doubles on the normal distribution. The naive, specialized, nor hard coding the result from WolframAlpha got me into the existing tolerance band.

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:52:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895413239  

📁 include/boost/math/distributions/extreme_value.hpp

```diff
 144 |+    if(e < tools::log_max_value<RealType>())
 145 |+       result = log(1/b) + e - exp(e);
 146 |+    // else.... result *must* be zero since exp(e) is infinite...
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:52:55 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816120547  

Shouldn't the result be -INF if `e < tools::log_max_value<RealType>()` doesn't hold?


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:53:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895413780  

📁 include/boost/math/distributions/chi_squared.hpp

```diff
 170 |+    }
 171 |+ 
 172 |+    return log(pdf(dist, chi_square));
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:53:28 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816120935  

Couldn't this rely on the default implementation since we do nothing special here?


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:54:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895414645  

📁 include/boost/math/distributions/gamma.hpp

```diff
 178 |+    else
 179 |+    {
 180 |+       result = -k*log(theta) + (k-1)*log(x) - log(tgamma(k)) - (x/theta);
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:54:21 UTC  
> Updated_at: 2022-02-28 17:54:22 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816121584  

lgamma here.

> Username: jzmaddock  
> Created_at: 2022-04-18 16:06:25 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r852224450  

You still have log(tgamma(k)) rather than lgamma(k), this is probably the reason this didn't work with type float (which is to say tgamma would overflow much too easily?).

> Username: mborland  
> Created_at: 2022-04-18 16:15:25 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r852230222  

I'll go through and replace them. I realize now that I was just replacing `tgamma` with `lgamma` and could not figure out why it was failing. Guess I should have re-read the docs sooner.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:57:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895417648  

📁 include/boost/math/distributions/exponential.hpp

```diff
 136 |+ 
 137 |+    RealType lambda = dist.lambda();
 138 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:57:20 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816123816  

We're going to have issues with copying over the special case handling in all of these I think, we may need to think about this, but to be consistent with taking the log of the pdf, we should start with result = -INF for these special cases.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:57:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895418259  

📁 include/boost/math/distributions/extreme_value.hpp

```diff
 132 |+    RealType a = dist.location();
 133 |+    RealType b = dist.scale();
 134 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:57:55 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816124297  

Special cases should probably be -INF again.


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:58:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895418589  

📁 include/boost/math/distributions/gamma.hpp

```diff
 160 |+    RealType theta = dist.scale();
 161 |+ 
 162 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:58:15 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816124513  

-INF


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:59:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895419689  

📁 include/boost/math/distributions/inverse_gamma.hpp

```diff
 208 |+    RealType scale = dist.scale();
 209 |+ 
 210 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:59:19 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816125300  

-INF for all the special cases I *think*.  Need to change the `return 0;` to `return result;` as well.


---

## Review 12 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 17:59:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895419867  

📁 include/boost/math/distributions/inverse_gamma.hpp

```diff
 231 |+    }
 232 |+ 
 233 |+    return shape * log(scale) + (-shape-1)*log(x) - log(tgamma(shape)) - (scale/x);
```

> Username: jzmaddock  
> Created_at: 2022-02-28 17:59:29 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816125465  

lgamma

> Username: jzmaddock  
> Created_at: 2022-04-18 16:07:42 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r852225266  

I'm still seeing log(tgamma(shape)) rather than lgamma(shape) ?


---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 18:00:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895420442  

📁 include/boost/math/distributions/inverse_gaussian.hpp

```diff
 182 |+    RealType scale = dist.scale();
 183 |+    RealType mean = dist.mean();
 184 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 18:00:05 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816125884  

-INF.


---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 18:01:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895421918  

📁 include/boost/math/distributions/laplace.hpp

```diff
 157 |+ 
 158 |+    // Checking function argument
 159 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 18:01:35 UTC  
> Updated_at: 2022-02-28 18:01:36 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816126971  

-INF, also a literal zero below to replace.


---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 18:02:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895422547  

📁 include/boost/math/distributions/normal.hpp

```diff
 177 |+    static const char* function = "boost::math::logpdf(const normal_distribution<%1%>&, %1%)";
 178 |+ 
 179 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 18:02:12 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816127476  

-INF and another literal zero.


---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 18:03:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895423367  

📁 include/boost/math/distributions/poisson.hpp

```diff
 293 |+       RealType mean = dist.mean();
 294 |+       // Error check:
 295 |+       RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 18:03:04 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816128067  

-INF


---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 18:03:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895423573  

📁 include/boost/math/distributions/poisson.hpp

```diff
 312 |+       if(k > 0 && mean > 0)
 313 |+       {
 314 |+         return -log(tgamma(k+1)) + k*log(mean) - mean;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 18:03:17 UTC  
> Updated_at: 2022-02-28 18:03:18 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816128223  

lgamma


---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 18:03:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895424131  

📁 include/boost/math/distributions/rayleigh.hpp

```diff
 129 |+ 
 130 |+    const RealType sigma = dist.sigma();
 131 |+    RealType result = 0;
```

> Username: jzmaddock  
> Created_at: 2022-02-28 18:03:49 UTC  
> Updated_at: 2022-02-28 18:03:50 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816128600  

-INF and another literal zero.


---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-28 18:51:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895471613  

📁 include/boost/math/distributions/weibull.hpp

```diff
 187 |+    }
 188 |+    
 189 |+    result = log(shape * pow(scale, -shape) * pow(x, shape - 1)) - pow(x / scale, shape);
```

> Username: jzmaddock  
> Created_at: 2022-02-28 18:51:21 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816162604  

Is there any reason not to expand this right out to:  
  
`log(shape) - shape * log(scale) + log(x) * (shape - 1) - pow(x / scale, shape);`


---

## Comment 20

> Username: jzmaddock  
> Created_at: 2022-02-28 18:59:52 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1054566646  

There's a few comments above, I haven't checked the details of the formulas, and am replying on your tests.  
  
We should also add logpdf to the DistributionConcept checks in test/compile_test/test_compile_result.hpp  
  
Otherwise, looks good, much thanks for taking this on!

---

## Review 21 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-28 21:44:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895625706  

📁 test/test_arcsine.cpp

```diff
 303 | 
 304 |+     // Log PDF
 305 |+     BOOST_CHECK_CLOSE_FRACTION(logpdf(arcsine_01, 0.000001), static_cast<RealType>(5.7630258931329868780772138043668005779060097243996L), tolerance);
```

> Username: NAThompson  
> Created_at: 2022-02-28 21:44:25 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816273119  

One thing I worry about these tests is that if they fail at some later date, it's not clear why (say) `5.7630258931329868780772138043668005779060097243996L` is the correct value. Is there a property that can be tested instead?


---

## Review 22 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-28 21:45:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/762#pullrequestreview-895626753  

📁 test/test_gamma_dist.cpp

```diff
  95 |+          gamma_distribution<RealType>(shape, scale),                             // distribution.
  96 |+          x),                                                                     // random variable.
  97 |+          log(boost::math::pdf(gamma_distribution<RealType>(shape, scale), x)),   // PDF
```

> Username: NAThompson  
> Created_at: 2022-02-28 21:45:34 UTC  
> Url: https://github.com/boostorg/math/pull/762#discussion_r816273858  

These are the sort of properties I'm thinking of--namely using two different implementations to compute the same thing. It's immediately obvious from this one that if the test fails something is *definitely* wrong.


---

## Comment 23

> Username: mborland  
> Created_at: 2022-04-17 19:19:53 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1100935427  

@jzmaddock Your review comments have been addressed; I apologize for the delay. I also ran the sonar lint and clang-tidy combs through this as suggested by @ckormanyos.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2022-04-18 16:12:32 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1101532290  

Hi Matt, thanks again for this, I just spotted a couple of location where you're still using `log(tgamma(x))` rather than `lgamma(x)` but other than that it all looks good to go to me.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2022-04-18 16:24:44 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1101542055  

@HDembinski: Looks like this is basically finished. Let us know if you hit any bugs in the usage.

---

## Comment 26

> Username: mborland  
> Created_at: 2022-04-19 01:45:39 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1101912216  

CI is clean so merging and closing linked issue.

---

## Comment 27

> Username: HDembinski  
> Created_at: 2022-04-21 08:34:07 UTC  
> Updated_at: 2022-04-21 08:34:24 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1104873868  

Thanks for this. It looked fine, but perhaps you can remove some of the now duplicated code between pdf and logpdf. For some distributions (poisson, normal, exponential), the pdf can be computed from the logpdf.

---

## Comment 28

> Username: jzmaddock  
> Created_at: 2022-04-21 10:01:16 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1104993420  

>Thanks for this. It looked fine, but perhaps you can remove some of the now duplicated code between pdf and logpdf. For some distributions (poisson, normal, exponential), the pdf can be computed from the logpdf.  
  
Technically yes, but would tend to loose precision due to cancellation error in the logpdf calculation?

---

## Comment 29

> Username: NAThompson  
> Created_at: 2022-04-21 14:44:13 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1105316485  

> Technically yes, but would tend to loose precision due to cancellation error in the logpdf calculation?  
  
Yeah I'd like to see an ulps plot before going for that . . . also `log` and `exp` are not particularly cheap.

---

## Comment 30

> Username: HDembinski  
> Created_at: 2022-04-21 17:59:12 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1105544433  

Fair enough, but you can do it at least for the exponential distribution, no? In case of the normal, you may be right. In case of Poisson, I don't know.

---

## Comment 31

> Username: NAThompson  
> Created_at: 2022-04-21 18:48:02 UTC  
> Url: https://github.com/boostorg/math/pull/762#issuecomment-1105631636  

> Fair enough, but you can do it at least for the exponential distribution, no?   
  
For the rate λ, we'd have log(pdf(x)) = log(λ) - λx, and then exp(log(pdf)) = exp(log(λ) - λx). That gives two transcendental function calls over 1 using the code-duplicated λexp(-λx). Losing (or gaining!) precision on the exp(log(λ)) also seems possible, although generally you have half-ulp guarantees with those functions. Maybe I'm overthinking it but I just feel like an ulps plot would help convince me.

---
