# #1120 - Skew normal distribution PPF incorrect for high skew [Closed]

> Username: dschmitz89  
> Created at: 2024-04-17 20:52:05 UTC  
> Updated at: 2024-05-05 12:41:49 UTC  
> Closed at: 2024-05-03 16:08:29 UTC  
> Url: https://github.com/boostorg/math/issues/1120  

This showed up in SciPy: https://github.com/scipy/scipy/issues/20124  
  
Minimal Python reproducer:  
  
```python  
  
from scipy.stats import skewnorm  
skewnorm.ppf(0.01, 500)  # 500 = skewness parameter  
  
RuntimeWarning: Error in function boost::math::quantile(const skew_normal_distribution<d>&, %1%): Unable to locate solution in a reasonable time: either there is no answer to quantile or the answer is infinite.  Current best guess is %1%  
-> 4.833181656374989e+142  
```   
  
SciPy recently updated to Boost's develop branch at [this commit](https://github.com/boostorg/math/tree/15c40fae1610fd83728d75e96d7377fe55f976f7), which did not solve this issue but only changed the error message to the one above.  
  
Thanks in advance!

---

## Comment 1

> Username: mborland  
> Created at: 2024-04-18 06:32:24 UTC  
> Url: https://github.com/boostorg/math/issues/1120#issuecomment-2063098823  

Confirmed with reproducer:  
  
```  
#include <boost/math/distributions/skew_normal.hpp>  
#include "math_unit_test.hpp"  
  
int main()  
{  
    using scipy_policy = boost::math::policies::policy<boost::math::policies::promote_double<false>>;  
    constexpr auto q = 0.012533469508013;  
      
    boost::math::skew_normal_distribution<double, scipy_policy> dist(0, 1, 500);  
    CHECK_ULP_CLOSE(0.009999999999999898, boost::math::cdf(dist, q), 1);  
    CHECK_ULP_CLOSE(q, boost::math::quantile(dist, 0.01), 1);  
  
    return boost::math::test::report_errors();  
}  
```

---

## Comment 2

> Username: mborland  
> Created at: 2024-04-18 06:52:43 UTC  
> Updated at: 2024-04-18 06:59:11 UTC  
> Url: https://github.com/boostorg/math/issues/1120#issuecomment-2063149161  

@jzmaddock here are the first couple steps. The guess looks pretty good but then it makes a massive first step and attempts to work downward taking 3-4 steps per order of magnitude.   
  
```  
Newton_raphson_iterate, guess = -0.061091221067366294206202326222410193, min = -1.7976931348623157081452742373170436e+308, max = 1.7976931348623157081452742373170436e+308, digits = 53, max_iter = 200  
  
Newton iteration 1, delta = -3.8833117392140707250772683903547802e+202, residual = -0.010000000000000000208166817117216851  
  
Newton iteration 2, delta = 1.9416558696070353625386341951773901e+202, residual = 0.98999999999999999111821580299874768  
  
Newton iteration 3, delta = 9.7082793480351768126931709758869504e+201, residual = 0.98999999999999999111821580299874768  
  
Newton iteration 4, delta = 4.8541396740175884063465854879434752e+201, residual = 0.98999999999999999111821580299874768  
  
Newton iteration 5, delta = 2.4270698370087942031732927439717376e+201, residual = 0.98999999999999999111821580299874768  
```  
  
Increasing the max number of steps to 10000 this converges after 710 steps to the correct answer.

---

## Comment 3

> Username: NAThompson  
> Created at: 2024-04-19 20:58:36 UTC  
> Updated at: 2024-04-19 21:38:45 UTC  
> Url: https://github.com/boostorg/math/issues/1120#issuecomment-2067275057  

@mborland , @jzmaddock : The following Mathematica code gets the update for the Halley iterate-I wonder if it can be used to stabilize the "wildness" of the Newton iterate:  
  
<img width="915" alt="Screenshot 2024-04-19 at 13 55 43" src="https://github.com/boostorg/math/assets/5459618/d427ee78-2b51-4e6e-9b98-a8bf51b1a70f">  
  
(You can see that the simplification could've been just a bit better there-most of the bells cancel.) So each iteration will take 2 erf calls, one Owen's T call, and an exp.)  
  
I confess to quite enjoying the Halley iterate for scalar rootfinding problems. For example, if Φ'(x) = 0 at some point, Newton's method fails, but Halley's method can gracefully degrade to [Halley's irrational method](https://mathworld.wolfram.com/HalleysIrrationalFormula.html). And if the denominator 2Φ'(x)² - Φ(x)Φ''(x) in Halley's method becomes zero, we can gracefully degrade to a Newton step.  
  
I also note that we might be able to do a bit better on constraining the domain:  
  
https://github.com/boostorg/math/blob/15c40fae1610fd83728d75e96d7377fe55f976f7/include/boost/math/distributions/skew_normal.hpp#L684-L685  
  
We might be able to use the fact that the mean of the skew normal distribution can be expressed in terms of a computation that requires only an addition and multiplication. That would allow us to write:  
  
  
```cpp  
if (p >= 0.5) {  
    search_min = μ + ωδsqrt(2/π);  
} else {  
   search_max = μ + ωδsqrt(2/π)  
}  
```  
  
Maybe not the _tightest_ constraint, but perhaps better than the entire real line.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-04-20 08:30:16 UTC  
> Url: https://github.com/boostorg/math/issues/1120#issuecomment-2067603083  

Just looking at this now - that looks like a rather expensive way of computing derivatives, but more to the point, I have a hunch that for sufficiently high skewness you will always reach a point where the computation blows up: effectively the CDF becomes something like a square wave with zero derivative(s) either side of a near vertical crossover point.  I'm wondering if the TOMS748 algorithm might be a better choice here: it has robust root bracketing (provided you start the right side of zero).  It's a little worse than Newton in the smooth cases (typically ~ 12 iterations for double precision) but against that there's no need to compute roots?  I'm out today, but I'll try and experiment later...

---

## Comment 5

> Username: NAThompson  
> Created at: 2024-04-20 16:30:21 UTC  
> Url: https://github.com/boostorg/math/issues/1120#issuecomment-2067722890  

>  that looks like a rather expensive way of computing derivatives  
  
Well, the idea is that you have to compute these quantities just to evaluate the CDF _anyway_, so you might as well use them for the derivatives too. But I think you're probably right: This is a bracketing problem, not a convergence problem.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2024-04-20 18:45:48 UTC  
> Url: https://github.com/boostorg/math/issues/1120#issuecomment-2067753788  

TOMS748 gets that particular case in 20 iterations which looks pretty good, but I need to devise some better tests and make sure it doesn't blow up anywhere...

---

## Comment 7

> Username: maresb  
> Created at: 2024-05-05 12:41:37 UTC  
> Updated at: 2024-05-05 12:41:49 UTC  
> Url: https://github.com/boostorg/math/issues/1120#issuecomment-2094793170  

Hey, sorry I missed out on this conversation. I have some thoughts regarding this. I'm fairly convinced that Newton's method would work great on the logit-CDF. Unfortunately, as far as I can tell, there is no numerically stable implementation of skewnorm CDF in the literature. (For positive skew and mildly-negative x, there is catastrophic cancellation when subtracting the Owen's T, even accounting for the `skew -> 1/skew` transformation.)  
  
I think I know how to compute it stably, but I'm unfortunately slammed with other work at the moment.
