# #1141 Prevent passing denormals in calculation. [Merged]

> Username: jzmaddock  
> Created at: 2024-05-23 18:06:03 UTC  
> Updated at: 2024-06-09 16:07:47 UTC  
> Merged at: 2024-06-02 14:25:36 UTC  
> Closed at: 2024-06-02 14:25:36 UTC  
> Url: https://github.com/boostorg/math/pull/1141  

Refs https://github.com/scipy/scipy/issues/20693

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-24 10:34:32 UTC  
> Updated_at: 2024-06-02 13:07:18 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2129205741  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1141?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.32886%` with `1 lines` in your changes are missing coverage. Please review.  
> Project coverage is 93.71%. Comparing base [(`cf0d343`)](https://app.codecov.io/gh/boostorg/math/commit/cf0d3437574bfb0bfb2a91bb8a79b0218a8e48eb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`20f44d1`)](https://app.codecov.io/gh/boostorg/math/commit/20f44d1275334d7b8b513fd9d8331cccf5248841?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1141/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1141      +/-   ##  
===========================================  
+ Coverage    93.69%   93.71%   +0.02%       
===========================================  
  Files          772      774       +2       
  Lines        61168    61271     +103       
===========================================  
+ Hits         57311    57422     +111       
+ Misses        3857     3849       -8       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1141?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/concepts/std\_real\_concept.hpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fconcepts%2Fstd_real_concept.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2NvbmNlcHRzL3N0ZF9yZWFsX2NvbmNlcHQuaHBw) | `100.00% <ø> (ø)` | |  
| [...t/math/distributions/detail/hypergeometric\_pdf.hpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fdetail%2Fhypergeometric_pdf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2h5cGVyZ2VvbWV0cmljX3BkZi5ocHA=) | `96.62% <100.00%> (ø)` | |  
| [...h/distributions/detail/hypergeometric\_quantile.hpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fdetail%2Fhypergeometric_quantile.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2h5cGVyZ2VvbWV0cmljX3F1YW50aWxlLmhwcA==) | `96.84% <100.00%> (ø)` | |  
| [...lude/boost/math/distributions/non\_central\_beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_beta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfYmV0YS5ocHA=) | `91.96% <100.00%> (+0.16%)` | :arrow_up: |  
| [include/boost/math/distributions/non\_central\_t.hpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfdC5ocHA=) | `97.62% <100.00%> (-0.03%)` | :arrow_down: |  
| [...e/boost/math/quadrature/detail/exp\_sinh\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fquadrature%2Fdetail%2Fexp_sinh_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZGV0YWlsL2V4cF9zaW5oX2RldGFpbC5ocHA=) | `96.71% <100.00%> (+1.47%)` | :arrow_up: |  
| [...clude/boost/math/special\_functions/jacobi\_zeta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fjacobi_zeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2phY29iaV96ZXRhLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/exp\_sinh\_quadrature\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=test%2Fexp_sinh_quadrature_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leHBfc2luaF9xdWFkcmF0dXJlX3Rlc3QuY3Bw) | `99.13% <100.00%> (+0.05%)` | :arrow_up: |  
| [test/nc\_t\_pdf\_data.ipp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=test%2Fnc_t_pdf_data.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9uY190X3BkZl9kYXRhLmlwcA==) | `100.00% <ø> (ø)` | |  
| [test/tanh\_sinh\_quadrature\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree&filepath=test%2Ftanh_sinh_quadrature_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90YW5oX3NpbmhfcXVhZHJhdHVyZV90ZXN0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [4 more](https://app.codecov.io/gh/boostorg/math/pull/1141?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1141/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1141?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1141?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cf0d343...20f44d1](https://app.codecov.io/gh/boostorg/math/pull/1141?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: dschmitz89  
> Created_at: 2024-06-05 20:13:07 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2150883060  

@jzmaddock : this looks impressive! One question for the addition to scipy. Do you have an external source for reference values for the noncentral T PDF? I did not check the complete (large) testing diff, might have missed it.  
  
If not, I would implement the formula [currently used in scipy](https://github.com/scipy/scipy/pull/20785/files#diff-05a8c4679d3be51f74b5780fc7b6ffed171da2da48db469aae1a772a8451a4c9L7814) with the high precision mpmath python library.

---

## Review 3 [Commented]

> Username: dschmitz89  
> Created_at: 2024-06-05 20:15:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1141#pullrequestreview-2100201611  

📁 tools/non_central_t_pdf_data.cpp

```diff
  22 |+ struct nc_t_pdf_gen
  23 |+ {
  24 |+    mp_t operator()(mp_t v, mp_t mu, mp_t x)
```

> Username: dschmitz89  
> Created_at: 2024-06-05 20:15:21 UTC  
> Url: https://github.com/boostorg/math/pull/1141#discussion_r1628381782  

Ah, this might be arbitrary precision implementation boost style that generates the test values after all?


---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-06-06 08:23:48 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2151696376  

Nod.  I used the hypergeometric formula and multiprecision arithmetic for the new test values.  
  
One thing I should have mentioned: the integration method though accurate, is about 10x slower than the alternatives :(  Other than truncation to zero, there really is no alternative though...  as I'm typing though I'm just wondering if wolframalpha would generate a series for the integral...

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-06-07 04:40:05 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2153947175  

> just wondering if wolframalpha would generate a series  
  
I'm not all that familiar with this function but I've recently been doing a lot of computer algebra for Series and Pade approximants in other projects. Does anyone have a link to representation of the integral mentioned? I coould try for an approximation.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-06-07 08:33:40 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2154359715  

>I'm not all that familiar with this function but I've recently been doing a lot of computer algebra for Series and Pade approximants in other projects. Does anyone have a link to representation of the integral mentioned? I coould try for an approximation.  
  
Cool, it's:  
  
```  
integral y^v exp(-1/2 ((y - mu * x / sqrt(x * x + v)))^2) dy for x = 0 to infinity  
```  
  
Which is the integral part of this formula https://wikimedia.org/api/rest_v1/media/math/render/svg/0059c7e6e3afb0e4496fecdcb3b85296c162f065  
  
Ideally we need this whenever `y < 0` in which domain the result is expected to be tiny.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2024-06-07 17:10:43 UTC  
> Updated_at: 2024-06-07 17:14:35 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2155221327  

> try for an approximation  
  
The first thing I tried is the exponent part alone without integrating over $dy$, wondering if series expansion and subsequent integration would work. And it is normalized by dividing by the large exponent term at the end of the expression. If the series is continued in $x$, then integration of $y^{\nu}$ should be straightforward enough. It's been a long day, but @jzmaddock your intuition is correct, it seems like a series could do the trick.  
  
W-Alpha didn't do it. I needed a bit more power and I ended up doing this first try locally.  
  
```  
Normal[Series[Exp[-1/2 ((y - mu*x/Sqrt[x*x + v]))^2], {x, Infinity, 4}] / Exp[-(1/2) (y - mu)^2]]  
```  
  
Note that we use `Normal[]` to truncate the series.  
  
Here is a picture of the input and its output.  
  
![grafik](https://github.com/boostorg/math/assets/2404721/fa9391ff-5212-4935-a3eb-3a8bc0646759)

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-06-08 05:07:28 UTC  
> Updated_at: 2024-06-08 05:20:21 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2155812044  

OK I included the scaling by the exponential term added the multiplication by $y^{\nu}$ in the integrand and did the definite integral. It gives results like the following to Order $4$. See below.  
  
Is there A numerical example I could use to check the approximation to Order $4$  
  
![grafik](https://github.com/boostorg/math/assets/2404721/bce47d27-94d3-4d77-9e37-b36022c5ecf3)

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2024-06-08 05:52:42 UTC  
> Updated_at: 2024-06-08 06:23:38 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2155824806  

Actually, @jzmaddock I don't know if expanding at $x{\rightarrow}{\infty}$ was correct. Are the presumptions in my approximation above even correct?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2024-06-08 11:11:18 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2155992799  

> Actually, @jzmaddock I don't know if expanding at was correct. Are the presumptions in my approximation above even correct?  
  
Since we're really interested in x < 0, I would say expansion either at x == 0 or x = -infinity would be better?  
  
I also note that the curve being integrated, is extremely "pointy" - all the area is located in a tiny area - so this might be getting out of hand, but a series around the maxima (for y) would be optimal I guess?  
  
If we fix the SciPy test case, with x = -1, v = 8 and mu=16 then https://www.wolframalpha.com/input?i=x%5E8+exp%28%28%28x+-+16+*+-1+%2F+sqrt%281+%2B+8%29%29%29%5E2+%2F+-2%29%3B  shows the curve nicely, most of the peak in this case is outside of [0, INF] but that's not always the case.  Interestingly, with all the variables fixed like this, wolfram alpha gives a simple(-ish) closed form for the integral, and for the summit of the curve.  Does this help at all?  Possibly not, since evaluating the definite integral in this case is the difference between two large values?  
  
The best way to sanity check the series would be against the integral itself:  
  
```  
            boost::math::quadrature::exp_sinh<T, Policy> integrator;  
            // Remove this line to check just the integral part:  
            T integral = pow(v, v / 2) * exp(-v * mu * mu / (2 * (x * x + v)));  
            if (integral != 0)  
            {  
               integral *= integrator.integrate([&x, v, mu](T y)   
                  {  
                     T p;  
                     if (v * log(y) < tools::log_max_value<T>())  
                        p = pow(y, v) * exp(boost::math::pow<2>((y - mu * x / sqrt(x * x + v))) / -2);  
                     else  
                        p = exp(log(y) * v + boost::math::pow<2>((y - mu * x / sqrt(x * x + v))) / -2);  
                     return p;   
                  });  
            }  
```  
  
Extracted from non_central_t.hpp.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2024-06-08 12:19:08 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156015261  

Hi John (@jzmaddock) OK I am getting closer.  
  
I just tried:  
  
```  
Integrate[(y^nu) Exp[-((y - ((mu x)/Sqrt[x x + nu]))^2)/2], {y, 0, Infinity}]  
```  
  
and received a closed form answer.  
  
![grafik](https://github.com/boostorg/math/assets/2404721/20c161c3-a506-4a1f-91f1-9466c58a2287)

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2024-06-08 14:39:06 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156060569  

There seems to be something wrong there if the answer depends only on x^2 ?

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2024-06-08 14:44:55 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156062509  

I think it should have been `Integrate[ [x^v exp(-1/2 ((x - mu * t / sqrt(t^2 + v)))^2)], {x, 0, Infinity}]` but wolframalpha chokes on that.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2024-06-08 17:51:42 UTC  
> Updated_at: 2024-06-08 17:55:09 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156120034  

> think it should have been  
  
Geez this is challenging. Like this then...  
  
```  
Integrate[x^v Exp[-1/2 ((x - mu t/Sqrt[t^2 + v]))^2], {x, 0, Infinity}]  
```  
  
Whith the following answer.  
  
![grafik](https://github.com/boostorg/math/assets/2404721/942b471d-a061-4285-8ccd-3f2a20c7eb66)  
  
But I think there are some very large terms and this is numerically unstable. I haven't quite figured out what to do with that yet. I'll try off and onn. Does not seem to be critical. If I ever come up with something, I'll report.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2024-06-08 17:58:12 UTC  
> Updated_at: 2024-06-08 17:59:23 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156121863  

Then I evaluated the numerical point at the test case, but internally the computer algebra system enlarges the precision. So the cancellations do not influence the result. But I haven't figured out what to do at fixed precison.  
  
![grafik](https://github.com/boostorg/math/assets/2404721/36026636-576d-47e6-9b81-84d79ee82607)

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2024-06-08 18:17:33 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156128546  

It's a nightmare. Here are the two added terms in the result. The cancellations quench the result for machine precision.  
  
I wish I knew more about asymptotics and perturbation theory... Arrrggghhh  
  
![grafik](https://github.com/boostorg/math/assets/2404721/b8ac9cf5-9c2a-4adc-af7a-00f715533d2b)

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2024-06-09 10:22:51 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156430841  

Well the good news is that 2.29e-9 is the correct answer ;)  
  
But yes, you've basically re-invented the hypergeometric approximation, and the cancellation is indeed terrible :(  
  
We might well be out of ideas at this point, but I appreciate the effort!

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2024-06-09 11:17:54 UTC  
> Updated_at: 2024-06-09 11:18:54 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156449826  

> might well be out of ideas at this point  
  
I tried expanding in parameters - the first trick of asymptotics. Expanding in ${\mu}$ worked but still suffered from severe cancellations. Expansion in ${\nu}$ did not converge.  
  
You know, this would be a great spot for the infamous `double-double`? I don't know if you'd like to throw Multiprecision at it, but if you double the working precision, you get, as you know, the digits back. But Multiprecision doesn't quite work with standalone Math. Somewhere down the line, Math _could_ benefit from its own kind of a `double-double`.  
  
Other than that, I'm out of ideas also.

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2024-06-09 11:36:01 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156456086  

The issue with the hypergeomentrics is that when v is small, the largest term in the hypergeometric series is of the order `(mu^2)!` which will quite rapidly overflow.  And even if it doesn't you end up subtracting two arbitrarily large numbers :(  So I don't think even a double-double would do it, better to suck it up and use numerical integration I would guess.

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2024-06-09 12:31:48 UTC  
> Updated_at: 2024-06-09 12:32:04 UTC  
> Url: https://github.com/boostorg/math/pull/1141#issuecomment-2156514574  

> The issue with the hypergeomentrics is that when v is small, the largest term in the hypergeometric series is of the order (mu^2)! which will quite rapidly overflow. And even if it doesn't you end up subtracting two arbitrarily large numbers :( So I don't think even a double-double would do it, ...  
  
Indeed. I even tried expansion in ${\mu}$ and although the expansion was promising, the cancellation was also present.   
  
> better to suck it up and use numerical integration I would guess.  
  
Yes. Thanks John. I'll hang up my hat on this one now.

---
