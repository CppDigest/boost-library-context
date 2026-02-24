# #1203 - boost::math::quantile() incorrect for poisson_distribution [Open]

> Username: paulharris  
> Created at: 2024-10-03 10:11:25 UTC  
> Updated at: 2024-10-21 07:41:59 UTC  
> Url: https://github.com/boostorg/math/issues/1203  

Hi,  
  
We spotted an apparent bias in the inverse cumulative poisson distribution function,  
looking deeper, we found boost is generating different results when compared with scipy and statslib (https://github.com/kthohr/stats)  
  
The value of mean is not important, but for p < 0.5, the result is offset by -1  
  
ie:  
```  
double mean = 2719.13;  
double p = 0.49;  
double r_boost = boost::math::quantile(boost::math::poisson_distribution<>(mean), p);  
double r_stats = stats::qpois(p,mean);  
r_boost --> 2717  
r_stats --> 2718  
```  
  
In Scipy:  
```  
import scipy.stats  
mean = 2719.13  
r = 0.49  
print(scipy.stats.poisson.ppf(r, mean))  
printout --> 2718.0  
```  
  
Code:  
```  
// Alternative statistics library: statslib  
// https://github.com/kthohr/stats  
#include <boost/math/distributions/poisson.hpp>  
#include <stats.hpp>  
#include <iostream>  
  
double mean = 2719.13;  
  
void test_range( double a, double b )  
{  
   for (double p = a; p < b+0.0001; p += 0.01)  
   {  
      double r_boost = boost::math::quantile(boost::math::poisson_distribution<>(mean), p);  
      double r_stats = stats::qpois(p,mean);  
      double diff = r_stats - r_boost;  
      std::cout << "Mean:" << mean << " p:" << p << " Boost=" << r_boost << " statslib=" << r_stats << " difference=" << diff << (diff != 0 ? " DIFFERENT" : " same") << std::endl;  
   }  
   std::cout << std::endl;  
}  
  
int main()  
{  
   test_range(0.01, 0.04);  
   test_range(0.47, 0.52);  
   test_range(0.96, 0.99);  
   return 0;  
}  
```  
  
Output:  
```  
Mean:2719.13 p:0.01 Boost=2598 statslib=2599 difference=1 DIFFERENT  
Mean:2719.13 p:0.02 Boost=2612 statslib=2613 difference=1 DIFFERENT  
Mean:2719.13 p:0.03 Boost=2620 statslib=2621 difference=1 DIFFERENT  
Mean:2719.13 p:0.04 Boost=2627 statslib=2628 difference=1 DIFFERENT  
  
Mean:2719.13 p:0.47 Boost=2714 statslib=2715 difference=1 DIFFERENT  
Mean:2719.13 p:0.48 Boost=2715 statslib=2716 difference=1 DIFFERENT  
Mean:2719.13 p:0.49 Boost=2717 statslib=2718 difference=1 DIFFERENT  
Mean:2719.13 p:0.5 Boost=2719 statslib=2719 difference=0 same  
Mean:2719.13 p:0.51 Boost=2720 statslib=2720 difference=0 same  
Mean:2719.13 p:0.52 Boost=2722 statslib=2722 difference=0 same  
  
Mean:2719.13 p:0.96 Boost=2811 statslib=2811 difference=0 same  
Mean:2719.13 p:0.97 Boost=2818 statslib=2818 difference=0 same  
Mean:2719.13 p:0.98 Boost=2827 statslib=2827 difference=0 same  
Mean:2719.13 p:0.99 Boost=2841 statslib=2841 difference=0 same  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-10-03 14:37:24 UTC  
> Url: https://github.com/boostorg/math/issues/1203#issuecomment-2391591745  

Confirmed. Mathematica outputs 2718 for command `Quantile[PoissonDistribution[2719.13], 0.49]` and so on

---

## Comment 2

> Username: mborland  
> Created at: 2024-10-03 17:04:00 UTC  
> Updated at: 2024-10-03 17:05:32 UTC  
> Url: https://github.com/boostorg/math/issues/1203#issuecomment-2391906917  

In all failing cases we're rounding to floor in `inv_discrete_quantile`: https://github.com/boostorg/math/blob/develop/include/boost/math/distributions/detail/inv_discrete_quantile.hpp#L427, since the default policy is:  
```  
#define BOOST_MATH_DISCRETE_QUANTILE_POLICY integer_round_outwards  
```  
if we change the policy in poisson for p < 0.5 to `integer_round_upwards` the results match, but I'm not sure that's a correct fix. I believe @jzmaddock is on holiday right now, but I'm sure he will have a better answer when he's back.   
  
Edit: branch "1203" has a prebuilt test set

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-10-03 18:44:03 UTC  
> Url: https://github.com/boostorg/math/issues/1203#issuecomment-2392088774  

I'm (somewhat) back from holiday, but I don't have a good answer because there really isn't one, and the correct behaviour depends a lot on your use case.  To summarise, we are inverting a real-valued continuous function, but are expecting a discrete result, so the options are:  
  
1) round up.  
2) round down.  
3) round to nearest.  
4) don't round at all, and let the user decide.  
5) The slightly odd (but hopefully makes sense when you think about it) "round outwards" which is our default.  
  
The choices and rationale is explained here: https://beta.boost.org/doc/libs/1_82_0/libs/math/doc/html/math_toolkit/pol_tutorial/understand_dis_quant.html

---

## Comment 4

> Username: paulharris  
> Created at: 2024-10-04 01:21:21 UTC  
> Url: https://github.com/boostorg/math/issues/1203#issuecomment-2392608336  

The problem was observed when we plotted the results of uniformly sampling p across 0-1, computing the quantile, and then plotting on a chart.  
With the current behaviour, you get a kink in the line at the median.  
If you plot the effective histogram of the distribution of quantiles from this sample, you get this much more obvious dip at the median.  
  
I understand there is a choice, but it is inconsistent with other math packages, and can have other knock-on affects when users expect consistent results across the number space.  
  
![image](https://github.com/user-attachments/assets/26014e75-9d72-4c78-9a8e-164c184ef9a5)

---

## Comment 5

> Username: fancidev  
> Created at: 2024-10-21 07:21:35 UTC  
> Updated at: 2024-10-21 07:41:59 UTC  
> Url: https://github.com/boostorg/math/issues/1203#issuecomment-2425828018  

My thought model for the quantile of a non-continuous distribution is: if we draw many, many observations, what would be the empirical quantile? I would use that number as the population quantile.  
  
The result turns out to agree with the [quantile function](https://en.m.wikipedia.org/wiki/Quantile_function) defined on Wikipedia:  
  
> It is often standard to choose the lowest value, which can equivalently be written as $Q(p) = \inf \\{ x \in \mathbb{R} : p \le F(x) \\}$.  
  
This might correspind to `integer_round_up` of the rounding policy, though mathematically speaking I think there’s no “rounding” taking place here because the CDF is discontinuous. (It’s different from the situation where the CDF is continuous but its value is not representable exactly by a floating point number.)  
  
(Btw, the second graph (CDF zoom-in) on [this page](https://beta.boost.org/doc/libs/1_82_0/libs/math/doc/html/math_toolkit/pol_tutorial/understand_dis_quant.html) is a little suspicious — the CDF jumps at `n+0.5` in that graph, but it should jump at `n` if I’m not mistaken.)
