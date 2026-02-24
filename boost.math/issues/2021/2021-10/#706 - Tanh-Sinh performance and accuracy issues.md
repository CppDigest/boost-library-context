# #706 - Tanh-Sinh performance and accuracy issues [Closed]

> Username: Robert-van-Engelen  
> Created at: 2021-10-12 18:58:16 UTC  
> Updated at: 2022-01-16 17:35:29 UTC  
> Closed at: 2022-01-16 17:35:28 UTC  
> Url: https://github.com/boostorg/math/issues/706  

I've been evaluating different implementations of Tanh-Sinh and noticed that Boost's Tanh-Sinh may not perform as expected, often requiring many more function evaluations to achieve the same accuracy as other implementations that use IEEE 754 double.  
  
Tanh-Sinh integration with x=0,1 f(x) should produce the same result as x=0,1 f(1-x), but it appears that Boost's implementation treats the boundary x=0 differently than the boundary x=1. There can always be a small difference, but I'm curious why the difference is substantial for Boost's Tanh-Sinh. Consider for example the results for Tanh-Sinh with x=0,1 f(x)=1/sqrt(sin(pi*x)) where the second is applied to f(1-x):  
  
`auto f = [&](double x) { return 1/sqrt(sin(M_PI*x)); }`  
1.66925366746488 error=8.0534e-10 evaluations=586  
`auto f = [&](double x) { return 1/sqrt(sin(M_PI*(1.0-x))); }`  
1.66925366127015 error=3.39749e-10 evaluations=1172  
  
There is a singularity at x=0 and x=1, which should not be a problem for Tanh-Sinh, but notice that the second integrand takes twice as many evaluations.  
  
More problematic is integrating 1/sqrt(-log(1-x)) over x=0,1 which fails with an exception, even though 1/sqrt(-log(x)) works fine. Both are essentially the same, but reversing the x direction causes a failure with an exception.  
  
Another very problematic example: x^-0.8 integrates fine over x=0,1 but (1-x)^-0.8 takes 267448 evaluations (!) to produce a result with a larger error 10^-3 (!) than the reported 10^-7 by Boost, which is still larger than the given tol=10^-9:  
  
5 error=0 evaluations=74  
4.99681841329715 error=2.62587e-07 evaluations=267448  
  
All examples above are performed with tolerance=10^-9.  
  
The benchmark code:  
~~~  
#include <boost/math/quadrature/tanh_sinh.hpp>  
  
using namespace boost::math::quadrature;  
  
static const double tol = 1E-9;  
static int ev = 0;  
  
int main() {  
  
  auto f = [&](double x) { ++ev; return 1/sqrt(sin(M_PI*x)); };  
  //auto f = [&](double x) { ++ev; return 1/sqrt(-log(1-x)); };  
  //auto f = [&](double x) { ++ev; return pow(x, -0.8); };  
  
  // swap integration "direction", integrating g should be the same as integrating f  
  auto g = [&](double x) { ++ev; return f(1.0-x); };  
  
  double error;  
  
  ev = 0;  
  double Q1 = tanh_sinh<double>().integrate(f, 0.0, 1.0, tol, &error);  
  printf("%.15g error=%g evals=%d\n", Q1, error, ev);  
  
  ev = 0;  
  double Q2 = tanh_sinh<double>().integrate(g, 0.0, 1.0, tol, &error);  
  printf("%.15g error=%g evals=%d\n", Q2, error, ev);  
}  
~~~  
I've completed a more in-depth comparison and analysis with the assistance of math experts on this [HP Forum thread](https://www.hpmuseum.org/forum/thread-16549.html). We came up with several Tanh-Sinh improvements (tested in C++ and Python) published as a [PDF article](https://www.genivia.com/files/qthsh.pdf). Perhaps this is useful. Feel free to contact me for questions, comments or concerns.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-10-12 19:52:22 UTC  
> Updated at: 2021-10-12 19:53:20 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-941392490  

Your pdf looks nice; will attempt a more careful read later.  
  
The issue with the symmetric equivalents are arising from the *asymmetry* of floating point numbers. You'll need to develop the two-argument functor discussed in the docs to get around it. To wit, a singularity near zero can be more accurately evaluated than a singularity near 1, since the spacing between floating point numbers near zero is tiny compared to the floats near 1.  
  
Heads up: The `tanh_sinh` class is designed to be used in parallel, so the better way to do this is to cache the integrator:  
  
```cpp  
#include <boost/math/quadrature/tanh_sinh.hpp>  
  
using namespace boost::math::quadrature;  
  
static const double tol = 1E-9;  
static int ev = 0;  
  
int main() {  
  auto integrator = tanh_sinh<double>();  
  auto f = [&](double x) { ++ev; return 1/sqrt(sin(M_PI*x)); };  
  //auto f = [&](double x) { ++ev; return 1/sqrt(-log(1-x)); };  
  //auto f = [&](double x) { ++ev; return pow(x, -0.8); };  
  
  // swap integration "direction", integrating g should be the same as integrating f  
  auto g = [&](double x) { ++ev; return f(1.0-x); };  
  
  double error;  
  
  ev = 0;  
  double Q1 = integrator.integrate(f, 0.0, 1.0, tol, &error);  
  printf("%.15g error=%g evals=%d\n", Q1, error, ev);  
  
  ev = 0;  
  double Q2 = integrator.integrate(g, 0.0, 1.0, tol, &error);  
  printf("%.15g error=%g evals=%d\n", Q2, error, ev);  
}  
```

---

## Comment 2

> Username: Robert-van-Engelen  
> Created at: 2021-10-12 21:22:03 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-941583743  

Thanks for the quick response.  
  
> To wit, a singularity near zero can be more accurately evaluated than a singularity near 1, since the spacing between floating point numbers near zero is tiny compared to the floats near 1.  
  
Yes with respect to f(x) when x is close to 0 allowing f(x) to be more accurate (small x deltas that can get really tiny close to x=0), but no when it comes down to the convergence and limit test accuracies, especially in the presence of singularities. The PDF addresses this in more detail than I should write down here. We spent quite a bit of time evaluating and analyzing methods to improve CPU performance as well as the impact of decisions with respect to the integration boundaries and handling singularities, verifying the improvements not only with challenging examples, but also with hundreds of integrands.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-10-13 10:32:47 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-942160529  

Thanks for this: having concrete examples to work on is always useful.  I'll try to look into this some more later as well.

---

## Comment 4

> Username: NAThompson  
> Created at: 2021-10-13 16:25:22 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-942474849  

Yeah, I'm excited to grapple with these ideas. IIRC, we spent most of our effort trying to get the API right to allow for parallel calls to the integrator, and we don't actually have any performance tests for the actual integration . . .

---

## Comment 5

> Username: Robert-van-Engelen  
> Created at: 2021-10-14 00:52:21 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-942836419  

@jzmaddock you're welcome! I am excited to share these findings and excited to help out in any way I can.  
  
PS. I made a small addition to the PDF article by adding a table of the final qthsh implementation compared to mpmath and Boost Math. Parts of this table occur in fragments earlier, but it is nicer to have a final table.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-10-14 19:11:47 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-943647093  

We need to work through these one at a time, but starting with:  
  
>More problematic is integrating 1/sqrt(-log(1-x)) over x=0,1 which fails with an exception, even though 1/sqrt(-log(x)) works fine. Both are essentially the same, but reversing the x direction causes a failure with an exception.  
  
The exception for the `1/sqrt(-log(1-x))` case shouldn't be entirely unexpected - indeed the presence of the `1-x` should be a bit of a red flag.  In this case tanhsinh quickly generates values smaller than machine epsilon and so 1-x == 1 and a non-finite value is produced.  Integrating over [eps, 1] is one way to solve this this issue, though some area remains unexplored.  
  
We could simply ignore non-finite values as you point out other libraries do.  However, that leads to the possibility that we ignore important areas particularly where the exponent range of the evaluation type is small (as it is for double) leading to infinite results for `f(x)`.  Perhaps a better heuristic is to ignore non-finite results as long as the closest finite section is insignificant compared to the overall sum.  That would require a small change to our internal logic as currently for performance reasons, we check to non-finite results only at the end of each level.    
  
I notice in your paper, you allow singularities in the middle of the integration range by discarding non-finite values.  I'm not so sure about that: tanhsinh handles singularities at the end points rather well because that's where most of the points are.  In the middle of the park they're rather sparsely spaced, and I could easily see you missing large sections of area (of course this is always a risk).  By way of example, consider a case from a previous bug report (https://github.com/boostorg/math/issues/423), that of `f(x) = x / sqrt(x^2 - 0.5)` where so much of the area is next to the singularity at 0.5, that a significant area remains unexplored even in the range [0.5-0.5eps, 0.5].  In other words it's impossible to get close enough to the singularity to find the "missing" part of the integral.  
  
The reversed example `1/sqrt(-log(x))` is also interesting, I modified your code slightly to find the actual error as well as the estimate, and at double precision with a 10^-9 tolerance rather a lot of iterations are consumed:  
  
```  
1.77245383046188 error=1.51587e-09 evals=2216 found error=1.15341e-08  
```  
  
I note (only somewhat wryly ;) ) that had you followed the advice in the docs to not set the tolerance below root-epsilon, then you would have seen:  
  
```  
1.77245384065218 error=5.34961e-09 evals=74 found error=5.78483e-09  
```  
  
So, a lot fewer calls to f(x), and better accuracy too!  
  
Alternatively, keeping 1e-9 tolerance, and changing to double-extended precision yields:  
  
```  
1.7724538502300483998 error = 5.996e-10 evals = 98 found error = 3.811e-10  
```  
  
and at quad precision:  
  
```  
1.7724538509055160308 error = 2.981e-15 evals = 103 found error = 1.98e-18  
```  
  
So... your tolerance is too small, causing the integrator to thrash around looking for precision that's not there.  In point of fact we do have some anti-thrashing heuristics in place - they basically abort the integration if moving to a new level increases the error estimate twice.  In the case of this function, the error estimate increases once, and then reduces, but very very slowly, building up error all the time.  Changing this heuristic so that anything that results in less than a 20% reduction in error is regarded as "thrashing" then we get:  
  
```  
1.7724538313362 error=9.31597e-09 evals=293 found error=1.10408e-08  
```  
  
Which is much better.  This however, needs careful testing.  
  
Finally... I wonder if the cause of these numerical instabilities can be addressed at all?  In this particular case f(x) can presumably be calculated perfectly accurately with respect to x.  However, I note that there is a slight disconnect between our abscissa values and their weights (which is to say the abscissa locations have a slight error in their representations), I wonder if calculating the weights from the actual abscissa representations would improve things at all, or am I entirely barking up the wrong tree?

---

## Comment 7

> Username: Robert-van-Engelen  
> Created at: 2021-10-14 20:07:40 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-943689241  

> So... your tolerance is too small, causing the integrator to thrash around looking for precision that's not there. In point of fact we do have some anti-thrashing heuristics in place  
  
Unless the definition or applicability of tolerance differs from defining an error bound on the result (either abs error bound or relative error bound), I do not see a reason why a 10^-9 tolerance _generally_ causes issues with IEEE doubles. There can be anecdotal cases, but anecdotal cases should not define or limit generality, right? A 10^-9 tolerance is perfectly fine to achieve a result within that given error bound, without causing the system the thrash around. Please see the mpmath tests in the article performed with 10^-9 using floats with 15 digits (mp.dps=15) and WP-34S implementation (which requires setting the tolerance much higher, typically 10^-15 to bound errors below 10^-9) and also this work: https://newtonexcelbach.com/2020/10/29/numerical-integration-with-tanh-sinh-quadrature-v-5-0/  
  
> I notice in your paper, you allow singularities in the middle of the integration range by discarding non-finite values. I'm not so sure about that  
  
Not really. There is a note in the article that serves as a warning. It is up to the implementor to decide what to do:  
  
_**Important**: singularities anywhere on the integration domain are effectively interpolated, not only singularities at or close to the endpoints of the domain. As always, due diligence should be applied when integrating functions with singularities on the domain, by splitting the domain up into parts to avoid singularities and non-differentiable points in the domain._  
  
I simply left this part further alone in the article, and added this note to avoid any suggestion that singularities in the middle of the integration domain are OK. However, the singularities at the endpoints are best ignored, but not always for a few anecdotal cases. Evidence suggests it is _generally_ best given a generous number of test cases.

---

## Comment 8

> Username: Robert-van-Engelen  
> Created at: 2021-10-14 20:32:03 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-943708139  

To answer the technical parts.  
  
> The reversed example 1/sqrt(-log(x)) is also interesting, I modified your code slightly to find the actual error as well as the estimate, and at double precision with a 10^-9 tolerance rather a lot of iterations are consumed:  
>   
> 1.77245383046188 error=1.51587e-09 evals=2216 found error=1.15341e-08  
  
Not sure what you mean by modifying my code. The article shows these results which are comparable to your test run with 2216 evals:  
  
qthsh: steps=120 error=6e-9  
WP-34S: steps=25 error=4e-7  
mpmath: steps=427 error=1e-10  
BoostMath: steps=2216 error=3e-9  
  
This is with a given eps=10^-9 (but WP-34S requires eps=10^-15 see earlier comment).  
  
My goal is not to trash BoostMath. I use Boost a lot myself. There are opportunities to improve BoostMath. These outliers in BoostMath are odd and seem totally unnecessary.  
  
> The exception for the 1/sqrt(-log(1-x)) case shouldn't be entirely unexpected - indeed the presence of the 1-x should be a bit of a red flag. In this case tanhsinh quickly generates values smaller than machine epsilon and so 1-x == 1 and a non-finite value is produced. Integrating over [eps, 1] is one way to solve this this issue, though some area remains unexplored.  
  
Right. This was discussed publicly on the forum starting with this post:  https://www.hpmuseum.org/forum/thread-16549-post-145175.html?highlight=sqrt%28-log%281-x%29%29#pid145175  and also https://www.hpmuseum.org/forum/thread-16549-post-145471.html?highlight=sqrt%28-log%281-x%29%29#pid145471

---

## Comment 9

> Username: Robert-van-Engelen  
> Created at: 2021-10-14 20:51:20 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-943722047  

Perhaps this helps. To offer insights as to how I've conducted the empirical tests: attached is the spreadsheet that compares different optimization choices for qthsh tested with 818 integrals (there are also tests for exp-sinh which you can ignore). I only believe improvements when there is both theoretical and experimental evidence and the results can be experimentally verified.  
  
[qthsh-test.xlsx](https://github.com/boostorg/math/files/7349290/qthsh-test.xlsx)

---

## Comment 10

> Username: Robert-van-Engelen  
> Created at: 2021-10-15 02:26:44 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-943934101  

I don't want the discussion to be about qthsh but rather about improving Boost Math Tanh-Sinh. For starters, it helps to compare the two. I would like to understand the Boost Math Tanh-Sinh implementation decisions and when the decisions are better or when perhaps implementations decisions could be hampering performance.  
  
To compare the two, I ran the 818 integrals against Boost Math Tanh-Sinh with tol=10^-9 and compared the results to qthsh with the same tol=10^-9:  
  
| metric        | Boost Math | qthsh |  
| ------------- | ---------- | ----- |  
| average evals | 2134.4     | 105   |  
| number of nan | 39         | 4     |  
| failures      | 120        | 97    |  
  
A failure means that the absolute error of the result exceeds 10^-8, giving the method a 10x window for its estimated convergence error. Some failures should be expected due to periodicity or non-differentiability of an integrand, i.e. Tanh-Sinh is not applicable or can be just lucky to produce a result. There are some of these among the 818 test cases.  
  
Observe that:  
- The number of Boost Math Tanh-Sinh NaNs produced is quite high, most can be successfully integrated.  
- The number of Boost Math Tanh-Sinh function evaluations is very high compared to other Tanh-Sinh implementations, not only compared to qthsh.  
  
Attached is the sheet with the results:   
[boost-test.xlsx](https://github.com/boostorg/math/files/7350420/boost-test.xlsx)  
  
The code I've used parameterized with $1~$5 for f(), a, b, tol, exact:  
~~~  
#include <boost/math/quadrature/tanh_sinh.hpp>  
#include <stdio.h>  
#include <stdlib.h>  
#include <math.h>  
#define pi M_PI  
#define sign(x) ((x>0)-(x<0))  
#define csc(x) (1/sin(x))  
#define csch(x) (1/sinh(x))  
#define sec(x) (1/cos(x))  
#define sech(x) (1/cosh(x))  
#define cot(x) (1/tan(x))  
#define coth(x) (1/tanh(x))  
  
using namespace boost::math::quadrature;  
  
static int ev = 0;  
  
double f(double x) { ++ev; return $1; }  
  
int main() {  
  double s, e = 0;  
  try {  
    s = tanh_sinh<double>().integrate(f, $2, $3, $4, &e);  
  }  
  catch (std::exception&) {  
    s = 0.0/0.0;  
  }  
  printf("%.16g,%d,%d,%g,%g\n", s, 0, ev, e, fabs(s-($5)));  
}  
~~~  
The qthsh code is as reported in the article.  
  
With respect to singularities in the middle of the domain, adding a check to the code can catch them effectively. Perhaps use the average of failed points in the middle to determine to quit?

---

## Comment 11

> Username: jzmaddock  
> Created at: 2021-10-17 18:29:35 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-945172453  

@Robert-van-Engelen : many thanks for the spreadsheet, that's a bit of a treasure trove of test cases ;)  
  
> Unless the definition or applicability of tolerance differs from defining an error bound on the result   
  
Somewhat: you have a factor of 10 fudge-factor in your termination condition that we don't have.  
  
In effect we have abrogated responsibility in the way we use the tolerance parameter - it's not a error target, but a termination condition.  That's why we recommend using the sqrt of your target as a tolerance - though the issue is that this does not always work.  I do note that Michalski & Mosig make the same recommendation though.  That not withstanding, I do have some ideas for how to improve things here so they're more obvious / user friendly.  
  
The other main difference I *think* is Mosig's variation on the tanh-sinh weights and abscissa calculation.  It seems like from what you're saying in your paper that Mosig's variation reaches slightly further out into the tails than standard tanh-sinh?  I note that Mosig has tried a "triple-exponential" transform, but that this was one step too far, so I guess it's about finding the best sweet spot.  
  
This is exemplified by your `f(x) = x^-p` test case: your code does very noticeably better for moderate p.  However, by the time we reach p = 0.99, even your qthsh only gets a few digits correct (Boost gets none!), and for p = 0.999 both routines are hopelessly out.  I suspect in these cases the routines are simply not finding important area near the origin, and terminating prematurely.  One can imagine I guess, a more sophisticated routine, that checks to see if the unexplored area next to an endpoint is on an increasing trajectory, and if so recursively exploring that area further.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2021-10-18 18:48:44 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-946063758  

I've created a branch, with Roberts test data, referenced above for experimenting with improvements.

---

## Comment 13

> Username: NAThompson  
> Created at: 2021-10-18 20:27:46 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-946139414  

>  I note that Mosig has tried a "triple-exponential" transform, but that this was one step too far, so I guess it's about finding the best sweet spot.  
  
It has been proved in Ooura's papers that double-exponential is the faster converging, and triple exponential converges slower.

---

## Comment 14

> Username: Robert-van-Engelen  
> Created at: 2021-10-19 20:31:26 UTC  
> Updated at: 2021-10-19 20:32:58 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-947082336  

> @jzmaddock I've created a branch, with Roberts test data, referenced above for experimenting with improvements.  
  
Most of the test cases are from this [link](https://newtonexcelbach.com/2020/10/29/numerical-integration-with-tanh-sinh-quadrature-v-5-0/) in the article, which should get most of the credit. I just spent time exploring the Mosig method and optimized its implementation with feedback from folks participating in the HP forum discussion.  
  
In addition to the sheets with results, I also wrote some bash scripts to compile the C++ code and run the tests automatically:  
`$ ./boost-test.sh < qthsh-test.csv > temprun.csv`  
The `temprun.csv` is then simply imported in the sheet. The three files in the zip you'll need:  
[boost-test.zip](https://github.com/boostorg/math/files/7376331/boost-test.zip)  
   
If I were to improve qthsh, I'd add checks to allow singularities close to the endpoint but not in the middle of the integration range, unless perhaps the user specifies an option to override and permit singularities anywhere. How close to the endpoints? That's a good question.  
  
> In effect we have abrogated responsibility in the way we use the tolerance parameter - it's not a error target, but a termination condition. That's why we recommend using the sqrt of your target as a tolerance - though the issue is that this does not always work. I do note that Michalski & Mosig make the same recommendation though. That not withstanding, I do have some ideas for how to improve things here so they're more obvious / user friendly.  
  
I had decided that a simple termination condition is likely best and intuitive, based on the excellent work on Tanh-Sinh for WP-34S, thinking that the user just wants the integral with N digits precision or close enough. The qthsh convergence test is different though, to bound the absolute error _and_ the relative error given the specified tolerance, depending on the magnitude of the integration result, i.e. if the result |_I_|<<1 this hybrid test puts emphasis on bounding the relative error. Other criteria can be used of course, as long as it is documented, I suppose.  
  
> The other main difference I think is Mosig's variation on the tanh-sinh weights and abscissa calculation. It seems like from what you're saying in your paper that Mosig's variation reaches slightly further out into the tails than standard tanh-sinh?  
  
Mosig's variation does not push the points as quickly towards the endpoints, so it appears to capture slightly more points in the middle, rather. I think this helps to improve cases that don't ideally work as well with Tanh-Sinh. Furthermore, the Tanh-Sinh overhead to produce the abscissas and weights is low. That helps to speed up the integration on slower machines, e.g. calculators. Otherwise, producing the points could be more costly than evaluating _f_ on each point, which can be quite noticeable on slower machines.  
  
I should also add that one might be perfectly content if Tanh-Sinh gives up after 500~2000 or so points, when convergence appears to be slow. At that stage it is clear at that the method fails for the given integrand. In this way, we don't force the method onto periodic or other non-holomorphic functions, because there is no guarantee that the result lies within a given error bound (the number of points in the middle may be too sparse); a different integration method should be used instead. Of course, it is debatable what is best, because the user may have different intentions.

---

## Comment 15

> Username: NAThompson  
> Created at: 2021-10-19 21:08:44 UTC  
> Updated at: 2021-10-19 21:14:49 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-947106284  

>  The qthsh convergence test is different though, to bound the absolute error and the relative error given the specified tolerance, depending on the magnitude of the integration result, i.e. if the result |I|<<1 this hybrid test puts emphasis on bounding the relative error.   
  
I put the emphasis on using the condition number of integration to define the tolerance parameter; see [Corless, A Graduate Introduction to Numerical Methods](https://www.springer.com/gp/book/9781461484523). This is a pseudo-relative error bound, in the sense that the denominator is actually the L1 norm of the integral, and not the absolute value of the integral, which would be the normal way to define relative error. I contend that this is the correct way to think about numerical integration.  
  
> I should also add that one might be perfectly content if Tanh-Sinh gives up after 500~2000 or so points, when convergence appears to be slow.   
  
I believe that there is an opportunity there: we could use the condition number of summation on a given level to define an interval in which the true sum lies. If the previous estimate of the integral also lies in this interval, then there is really no point in going on.  
  
> Otherwise, producing the points could be more costly than evaluating f on each point, which can be quite noticeable on slower machines.  
  
This was one reason why we have all the thread safety stuff in our implementation. We are imagining the user computing lots of integrals, say to compute a stiffness matrix, and evaluating them in parallel from a single computation of nodes and weights. We could cache nodes and weights at (say) double precision, but since one of the primary goals of tanh-sinh is to get to arbitrary precision, this gets awkward.

---

## Comment 16

> Username: Robert-van-Engelen  
> Created at: 2021-10-20 19:22:18 UTC  
> Updated at: 2021-10-22 21:09:10 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-947966959  

@NAThompson thanks for the explanation which makes a lot of sense.  
  
There is another thing I'd like to add that can be useful, which came up as a response to a question about integration accuracy on the HP forum: can the integration method be _inherently_ noisy? E.g. due to internal float inaccuracies to compute abscissas and weights? Simple weights pose no problem, e.g. Simpson, Romberg are exact for (low order) polynomials. Weights derived from table-based methods, such as Gauss-Kronrod may collect small errors in the weight calculations. So I was curious to test this for Tanh-Sinh with a simple constant function integration ∫2dx for x=-10..10 with eps=10^-6 tolerance or about _to make sure we get comparable number of evaluation points_ for each method:  
  
- Gauss-Kronrod G10K21 with IEEE 754 doubles = 40.000 000 000 000 007.  
- WP-34S Tanh-Sinh C version with IEEE 754 double: 39.999 999 999 987 885 (23 points)  
- qthsh Tanh-Sinh with IEEE 754 double: 40.000 000 000 000 057 (29 points)  
- Boost Math Tanh-Sinh with IEEE 754 double: 40.000 000 000 001 464 (25 points)  
  
This is by no means a scientific experiment or should be considered conclusive, because I just picked a constant function as the simplest to test (and so I ran into this bug #702). Also pushing smaller eps will have a corrective effect and the error is smaller than the requested accuracy, so no real harm done! However, this observation may help to evaluate the intrinsic numerical stability and accuracy of the abscissa and weight calculations of the method for larger eps, e.g. test with eps=10^-2 to 10^-9. The qthsh abscissas _a_+_x_ and _b_-_x_ with _x_=_dr_ and weights _w_=(_t_+1/_t_)_r_/(1+_u_) with _r_=2 _u_/(1+_u_) are produced as more tightly coupled pairs depending both on _r_, i.e. without involving other functions like the WP-34S code uses _x_=_dr_ with _r_=sqrt(_ecs_^2_-1)/_ecs_ and _w_=1/(_ecs_^2) with _ecs_=cosh(pi/2sqrt(_ch_^2-1)) see article. IMHO each arithmetic operation or function introduces an error. Longer chains of calculations produce larger errors. Catastrophic cancellation should be avoided at all cost, if possible. Note that qthsh _t_ and _u_ are positive, so no catastrophic cancellations cannot and should not occur in _t_+1 and 1+_u_.

---

## Comment 17

> Username: NAThompson  
> Created at: 2021-10-20 19:43:22 UTC  
> Updated at: 2021-10-20 21:07:02 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-947981636  

> There is another thing I'd like to add that can be useful, which came up as a response to a question about integration accuracy on the HP forum: can the integration method be inherently noisy?  
  
William Kahan reportedly mocked tanh-sinh as the "integration method that doesn't even integrate constants exactly", so a constant might be a poor test for what you're thinking about. Nonetheless, you can work out the perturbation on the  tanh-sinh weights and get a more sophisticated rounding model, but that doesn't by itself allow for catastrophic cancellation as the weights are all positive. (Of course, the integrand itself can be positive or negative, but that is dealt with by terminating the integration using the condition number of integration.)  
  
Incidentally, the reason why higher-order Newton-Cotes methods are viewed as unacceptable is because some of their weights are negative, meaning a well-conditioned integration problem can have a poorly conditioned quadrature sum.  
  
In our Monte-Carlo integration, we used Kahan summation to prevent the εn cond(integral) error growth, as this error growth is *faster* than the convergence of naive Monte-Carlo integration. It might be interesting to see how much overhead that costs in tanh-sinh. You expect for n function calls, you lose n cond(integral) ulps, whereas if you use Kahan summation on the quadrature sum, you lose cond(integral) ulps. It appears that best case, we have n = 25, so might be worth looking in to.

---

## Comment 18

> Username: Robert-van-Engelen  
> Created at: 2021-10-20 21:56:20 UTC  
> Updated at: 2021-10-21 02:16:58 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-948067950  

Yeah, in hindsight I think the constant function isn't a good choice to analyze noise. Checking with decreasing eps from 10^-2 to 10^-16 with increasing sets of points shows no monotonically decreasing errors and the three implementations tested do not show significant differences. It is to be expected with this formula. A trig function could work, but the fast DE quadrature convergence won't show much usefulness to try that either.  
  
What I meant with cancellation is just the effect on intermediate calculations like ecs^2_-1 and ch^2-1 when ecs or ch (both are cosh values) approach 1. However, the sqrt (and its use afterwards) of those will also be very small anyway, so those cancellations should not cause a major issue. Or did I miss something?  
  
I like the idea to look into Kahan summation. The thought to sum the values from small to large in absolute magnitude has crossed my mind before. Something I've explained before in the graduate classes I taught. But Kahan should be better as it doesn't require a sorted array.

---

## Comment 19

> Username: NAThompson  
> Created at: 2021-10-21 03:56:18 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-948235544  

> What I meant with cancellation is just the effect on intermediate calculations like ecs^2_-1 and ch^2-1 when ecs or ch (both are cosh values) approach 1. However, the sqrt (and its use afterwards) of those will also be very small anyway, so those cancellations should not cause a major issue. Or did I miss something?  
  
Bah, github needs to support mathjax; I'm having a hard time following.

---

## Comment 20

> Username: jzmaddock  
> Created at: 2021-10-21 11:37:01 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-948524426  

>There is another thing I'd like to add that can be useful, which came up as a response to a question about integration accuracy on the HP forum: can the integration method be inherently noisy?   
  
I think so yes, that's what I was alluding to above, but didn't phrase very well.  
  
I'm less concerned about the accuracy of the weights, than the accuracy of the abscissa locations - at the very least there must be a 0.5ulp error, so if f(x) is particularly ill-conditioned in a particular domain, then we get "noisy" values for f(x) back and the algorithm starts thrashing.  My suspicion, is that we already have a test case - your `f(x) = x^-p` for p close to but less than one.  I also suspect, but don't know how to prove just yet, that your choice of C = 1 rather than PI/2 is an inherent improvement in this area.

---

## Comment 21

> Username: NAThompson  
> Created at: 2021-10-21 16:05:37 UTC  
> Updated at: 2021-10-21 16:05:55 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-948760059  

> I'm less concerned about the accuracy of the weights, than the accuracy of the abscissa locations - at the very least there must be a 0.5ulp error, so if f(x) is particularly ill-conditioned in a particular domain, then we get "noisy" values for f(x) back and the algorithm starts thrashing.   
  
This is 100%, exactly correct. And it's even worse than that, because if the integrand is singular, we know the condition number *must* go infinite.  
  
Yesterday I was playing around with trying to get the most general error model for floating point quadrature, and noticed exactly what @jzmaddock points out: If you assume non-exact abscissas, you get conditioning errors as well as floating point roundoff error. But I could not figure out how to *exploit* this information to get a convergence criteria. We generally assume the integrand is a black box, so no derivatives. And if you *did* have derivative information, then it'd be more efficient to use a quadrature method which exploits this information.

---

## Comment 22

> Username: jzmaddock  
> Created at: 2021-10-21 16:08:35 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-948762982  

>My suspicion, is that we already have a test case - your f(x) = x^-p for p close to but less than one. I also suspect, but don't know how to prove just yet, that your choice of C = 1 rather than PI/2 is an inherent improvement in this area.  
  
Apparently not so.  Notwithstanding the fact that your routine does better when p is only moderately close to 1, the ultimate cause of issues here seems to be lack of exponent range.  
  
With p = 1 - 2^16 = 0.9999847412109375  
  
Boost, double precision gives:  
  
36.0337435165785 error=0 evals=891 found error=1817.74  
  
qthsh double precision gives:  
  
706.824324290567 error=8.76182e-05 evals=167264 found error=91.7189  
  
Now for the interesting stuff, if I use multiprecision types with more bits, but no more exponent range, then the boost routine just thrashes and gets nowhere, but using a type with 53-bits precision, but a stupidly large exponent range:  
  
using quad = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<53, boost::multiprecision::backends::digit_base_2, void>, boost::multiprecision::et_off>;  
  
Gives:  
  
65536.000000000014552 error = 1.851e-07 evals = 191 found error = 2.22e-16  
  
In other words it just goes straight to the result!

---

## Comment 23

> Username: jzmaddock  
> Created at: 2021-10-21 16:56:32 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-948804644  

At the risk of changing my mind yet again.... we should be able to integrate over an arbitrary [x, y] range, so this is not just about exponent range.  
  
There is another source of error here, which is the transformation of the range from [x,y] to [-1,1].  qthsh does this in the "obvious" way which results in f(x) never been called with values smaller than machine epsilon, where as we transform zero endpoints in a way which reaches right down to DBL_MIN.  There are obvious strengths and weaknesses to both.  All of which makes me wonder whether there is merit in a DE method with a native [0,1] range (tanh-exp ??).

---

## Comment 24

> Username: NAThompson  
> Created at: 2021-10-21 17:38:20 UTC  
> Updated at: 2021-10-21 18:13:07 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-948855221  

We might also get some wins by changing  
  
  
https://github.com/boostorg/math/blob/b0d1e4f760450c66bebfb0da6ec2cc9d7534bb5f/include/boost/math/quadrature/detail/tanh_sinh_detail.hpp#L338-L339  
  
to  
  
```cpp  
using std::fma;  
result_type y = yp + ym;  
if constexpr (std::is_floating_point_v<result_type>) {  
    sum = fma(y, w, sum);  
} else {  
   sum += y*w; // no fma for complex values.  
}  
```  
  
Presumably some people wouldn't see any difference if they use the appropriate compiler flags, but this could halve the error otherwise.

---

## Comment 25

> Username: jzmaddock  
> Created at: 2021-10-22 18:16:11 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-949861705  

@NAThompson , I think we have a bug: there is a disconnect when we move from row 7 (pre-computed data) to row 8 (computed on the fly).  The rows are doubling in size up to 384 at row 7 then instead of doubling to 786 they drop to 640 at row 8.  The result is that the error estimate which was halving at each level, goes up significantly at level 8 as many points at the extremes are missing.  
  
Found while testing f(x) = x^-0.9999847412109375.  
  
Thoughts on the best way to fix?

---

## Comment 26

> Username: jzmaddock  
> Created at: 2021-10-23 11:20:43 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-950137517  

OK, the original list of test cases are GPL, so they're in a separate repository : https://github.com/jzmaddock/tanhsinh-test-cases

---

## Comment 27

> Username: jzmaddock  
> Created at: 2021-10-23 11:25:49 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-950138065  

>I think we have a bug: there is a disconnect when we move from row 7 (pre-computed data) to row 8 (computed on the fly). The rows are doubling in size up to 384 at row 7 then instead of doubling to 786 they drop to 640 at row 8. The result is that the error estimate which was halving at each level, goes up significantly at level 8 as many points at the extremes are missing.  
  
Fixed in https://github.com/boostorg/math/pull/711

---

## Comment 28

> Username: NAThompson  
> Created at: 2021-10-23 15:41:38 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-950170297  

> OK, the original list of test cases are GPL, so they're in a separate repository : https://github.com/jzmaddock/tanhsinh-test-cases  
  
Wait, isn't this a pretty expansive definition of the GPL? It looks like you retyped the test cases . . .

---

## Comment 29

> Username: NAThompson  
> Created at: 2021-10-23 15:51:06 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-950171672  

> Fixed in #711  
  
Man, that bug is super subtle; only being slow for just a few test integrals, and still gets the right answer. Nice catch.

---

## Comment 30

> Username: jzmaddock  
> Created at: 2021-10-23 16:07:18 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-950174228  

> Wait, isn't this a pretty expansive definition of the GPL? It looks like you retyped the test cases . . .  
  
Well I ran them through a bunch of search and replaces to C++-ify them.  I have to be honest, I've asked other authors about such things before, and the response has always been "don't be daft, you can't copyright that, of course you can use those test cases".  In this case the author was very definite that that was not the case.  Anyhow we don't need that in the Boost repro for anything except our own convenience.  
  
@Robert-van-Engelen : you might need to make it clear in your paper that the code is GPL-infected (apologies if you have and I missed it).  
  
> Man, that bug is super subtle; only being slow for just a few test integrals, and still gets the right answer. Nice catch.  
  
You get similar issues with root finders - they keep working no matter what subtle bugs there may be -so it's quite hard to empirically test them.  Been here before in other words!

---

## Comment 31

> Username: Robert-van-Engelen  
> Created at: 2021-10-23 19:13:19 UTC  
> Updated at: 2021-10-23 19:23:09 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-950199684  

>  you might need to make it clear in your paper that the code is GPL-infected (apologies if you have and I missed it).  
  
FYI.  
  
The 21 test cases are not copied from the GPL source. The qthsh and quad code presented in the paper is not derived from GPL code either and can be freely used under the MIT license.  
  
Appendix A has a fragment of the DE code from the same URL, so that DE code is GPL I believe. I will add a note to the paper about the GPL parts in Appendix A and C, which both show some materials from that URL.  
  
The WP-34S python version of the Tanh-Sinh code is MIT licensed, see https://github.com/mcesar-rlacruz/py-double-exponential

---

## Comment 32

> Username: jzmaddock  
> Created at: 2021-10-28 15:44:56 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-953971678  

>The 21 test cases are not copied from the GPL source. The qthsh and quad code presented in the paper is not derived from GPL code either and can be freely used under the MIT license.  
  
My bad, thanks for the clarification!  
  
Must be time for a progress report, here's some headline numbers for how we're currently doing:  
  
|Library | Average #Calls per Integral | Median #Calls per Integral |  
|--------|----------------------------------|---------------------------------|  
|Boost Develop| 53857933 |147|  
|Boost PR 714|1914 |147|  
|qthsh|4681| 58|  
  
In other words the Boost.PR version now has very much better error handling, and actually does remarkably well at distinguishing noise from discovering new unexplored area.  However, as you can see from the median, qthsh does much better for well-behaved "easy" cases.  
  
Known differences between the two are:  
  
1) qthsh treats the tolerance as the target precision and obtains the termination condition by multiplying by 10.  Boost just treats the tolerance passed as a termination condition.  This is *not* the cause of better performance of qthsh for the easy cases though.  
2) qthsh sets the max # levels to 6, where as we default to 15.  For the purposes of the comparisons I upped qthsh to max 15 levels, which does make it's worst case performance (arguably artificially) much worse.  
3) qthsh sets the constant C to 1, where as we set it to PI/2.  I have experimented with changing this and it seemed to make no real difference, in fact changing it away from PI/2 only seemed to make things worse.  I'm not completely sure I haven't made a mistake in the coding here though (I just replaced all occurrences of PI/2 in the source with the new constant).  
  
Still to do:  
  
* check the error estimates are now better.  
* Add more (special-function based) integrals.  
* Investigate why qthsh is better for the easy cases.

---

## Comment 33

> Username: jzmaddock  
> Created at: 2021-10-28 18:23:49 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-954091961  

OK, one more comment before I (probably) leave this for a bit: here's an evil little function: `f(x) = 1/(a sqrt(pi)) exp(-(x/a)^2)` with say `a = 0.125`.  
  
Here's what I see over various integration ranges:  
  
|Range|Boost #calls|Boost ErrorFound|qthsh #Calls|qthsh ErrorFound|  
|-------|---------------|-------------------|--------------|---------------------|  
|[0,1000] |1172   | 4.44089e-16   |  3  | +INF  |  
|[-1,1000]|1655 | 4.44089e-16   |  3  | +INF |  
|[-1000,1000]|104254  |  1.03461e-07  | 55| 4.95159e-14|  
|[-1,1]|407 |0  | 55 |  2.72449e-13 |

---

## Comment 34

> Username: Robert-van-Engelen  
> Created at: 2021-10-28 19:09:21 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-954124074  

> qthsh sets the max # levels to 6, where as we default to 15. For the purposes of the comparisons I upped qthsh to max 15 levels, which does make it's worst case performance (arguably artificially) much worse.  
  
About limiting k <= 6 or 7 levels: this is not required, but the limit improves performance on slower machines when the fp precision is not high (not  better than double IEEE 754), so that we don't end up summing insignificant terms to the sum trying to converge when convergence is illusive.  
  
> - check the error estimates are now better.  
> - Add more (special-function based) integrals.  
> - Investigate why qthsh is better for the easy cases.  
  
I wonder if setting the left bound to 0 explains why difficult cases are better in Boost compared the qthsh's current -1 left bound? With a left bound of 0 one can continue all the way down to `DBL_MIN`. On the other hand, the weights become tiny. The small weighted terms added to the sum quickly become insignificant, unless most of the integral's mass is clustered around the left bound so that pushing very close to 0 will in fact help. But in general, won't you run into singularities way before hitting `DBL_MIN`? (i.e. assuming the function is not defined close to the left bound.)  
  
There are arguments for and against a 0 left bound. It depends on tradeoffs. A consequence of [0,1] bounds, the integration is non-symmetric wrt. reversing the x axis direction of integration, i.e. `A-x` when integrating from x = 0 to A or `-x` when integrating from x = -A to A. For most cases there isn't a difference. But for some there is a difference: reversing the direction should (ideally) produce the same integral or at least converge. It might be unexpected for users if that is not the case. I wonder how many functions require getting that close to `DBL_MIN`? If there are very few, is it still worthwhile to optimize specifically for those but then loose performance to evaluate more points _in general_?  
  
> OK, one more comment before I (probably) leave this for a bit: here's an evil little function: f(x) = 1/(a sqrt(pi)) exp(-(x/a)^2) with say a = 0.125.  
  
I wouldn't worry about some cases like this, because with Tanh-Sinh implementation differences one can get lucky or not when picking the "right" points. The qthsh implementation does not have a lower limit on the number of points to probe, so this can be expected! Same with Romberg picking 3 points if the 3 points are one a line!

---

## Comment 35

> Username: NAThompson  
> Created at: 2021-10-28 19:16:56 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-954129078  

> I wouldn't worry about some cases like this, because with Tanh-Sinh implementation differences one can get lucky or not when picking the "right" points.   
  
This reminds me of Kahan's spy function, which demo'd that no deterministic integration routine can accurately evaluate all integrals.

---

## Comment 36

> Username: Robert-van-Engelen  
> Created at: 2021-11-16 17:55:19 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-970521101  

@NAThompson Is there anything you want me to take a look at or help out?

---

## Comment 37

> Username: NAThompson  
> Created at: 2021-11-16 23:18:43 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-970806587  

Bah, I think the only thing that could help at this point is a translation of some of the original Japanese literature; e.g.  
  
Takahasi, Hidetosi. "Error estimation in the numerical integration of analytic functions." Rep. Comput. Centre Univ. Tokyo 3 (1970): 41-108.

---

## Comment 38

> Username: jzmaddock  
> Created at: 2021-11-17 17:57:54 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-971821643  

> Is there anything you want me to take a look at or help out?  
  
What I have on my HD right now (but not quite ready for public review yet), is a version of the Boost code which rigorously prunes the tails of trivially small values: the code makes many fewer function calls, though still not necessarily as few as qthsh.  At this point I would say the two routines are "different" rather than better or worse: qthsh can make fewer calls, but can also miss significant areas in the tails (examples to come).  What I'm doing now, is working through all the cases where the boost code has high error rates and figuring out what the issue is, at the end, I should have an interesting list (with examples) of archetypical cases where tanhsinh performs badly, some fixable, some not.

---

## Comment 39

> Username: NAThompson  
> Created at: 2021-11-17 18:46:04 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-971869075  

@jzmaddock : Will these ideas be useful for exp-sinh and sinh-sinh as well?

---

## Comment 40

> Username: genivia-inc  
> Created at: 2021-11-17 21:57:52 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-972113106  

In the end, I would expect a straightforward (but balanced) implementation like qthsh to not be able to beat Boost Tanh-Sinh. But right now, I'm not so sure when looking at the averages.  
  
Practically speaking, the focus of qthsh (and the WP-34S code to begin with) was to reduce CPU cost with a straightforward implementation to produce reasonably accurate results i.e. no bells and whistles e.g. deal with singularities more smartly when in the middle or optimize the transformation of the integration domain.  
  
There are plenty of things that can be further explored.  
  
For example, to push _both_ tails towards 0 instead of just the one in Boost Tanh-Sinh, I'd split the integration domain up into two parts and change variables such that each of the two parts has a 0 bound. That is, int_a,b f(x)dx = int_0,1 f(y)dy + int_0,1 f(z)dz with applicable y and z transformations. It would be interesting to see what the accuracy can be in this case, but also what the impact of integration is i.e. splitting up the domain in two can have benefits but may also have issues depending on the integrand. Didn't test this idea, so I don't know (yet).

---

## Comment 41

> Username: jzmaddock  
> Created at: 2021-11-18 12:03:05 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-972802764  

>In the end, I would expect a straightforward (but balanced) implementation like qthsh to not be able to beat Boost Tanh-Sinh. But right now, I'm not so sure when looking at the averages.  
  
It's not so simple, as there are design decisions to be made.  
  
Generally, qthsh performs well because it doesn't go too far out into the tails - this works brilliantly at low precision for bell shaped curves where most of the area is in the middle of the park, but can fail quite badly in some cases.  
  
Consider test case 36: `exp(1 - 1 / x) * cos(1 / x - 1) / x^2 for x = 0 to 1`  
  
At double precision and root-epsilon tolerance I see:  
  
```  
Boost: 0.499999999999999888977697537484 error=2.49e-14 evals=279 found error=2.22e-16  
qthsh: 0.499999999040809 error=4.77e-09 evals=202 found error=1.92e-09  
```  
  
This is with the current code on my HD, my apologies, I will push it soon, but I need to finish reviewing all the dodgy results first!  
  
Now up the precision to cpp_bin_float_50 (I'm using a trivially adapted template version of qthsh) and I see:  
  
```  
Boost: 0.5 error=7.83e-27 evals=731 found error=3.26e-48  
qthsh: 0.5 error=2.18e-26 evals=1058 found error=2.34e-26  
```  
  
If I up the tolerance to 1e-40 then we get:  
  
```  
Boost: 0.5 error=2.21e-48 evals=1438 found error=1.34e-50  
qthsh: 0.5 error=4.04e-41 evals=2335 found error=5.11e-41  
```  
  
So now Boost is thrashing around for no good reason, and qthsh is slowly getting there.  The reason is simply that qthsh is slower to get out into the tails than Boost.  
  
We can argue endlessly over design decisions here, but in general qthsh is missing a small section in the tails compared to Boost, often this has no effect and qthsh makes fewer function calls, but when there's noticeable area in the tails it leads to poorer found errors, and/or entirely missed areas.

---

## Comment 42

> Username: jzmaddock  
> Created at: 2022-01-16 17:35:28 UTC  
> Url: https://github.com/boostorg/math/issues/706#issuecomment-1013919060  

This should all be addressed in develop now.  As mentioned above there are different design decissions leading to differing accuracy/performance tradeoffs depening on the function.
