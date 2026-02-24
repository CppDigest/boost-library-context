# #203 - typo in documentation of binomial_distribution [Closed]

> Username: mikucionisaau  
> Created at: 2019-05-07 16:11:18 UTC  
> Updated at: 2019-05-13 17:36:10 UTC  
> Closed at: 2019-05-13 17:36:10 UTC  
> Url: https://github.com/boostorg/math/issues/203  

The documentation issue is trivial, but misleading: I followed it blindly and got some weird results, had to debug... :-)  
On the following page:  
https://www.boost.org/doc/libs/1_70_0/libs/math/doc/html/math_toolkit/dist_ref/dists/binomial_dist.html  
Table 5.1 says about quantile non-member function:  
  
> The **greatest** number of successes that may be observed from n trials with success fraction p, at probability P. Note that the value returned is a real-number, and not an integer. Depending on the use case you may want to take either the floor or ceiling of the result. For example:  
> `quantile(binomial(n, p), P)`  
  
Actually it must be the **smallest** instead of **greatest**.  
  
Likewise the next row should also be fixed by changing the **smallest** into **greatest**:  
  
> The **smallest** number of successes that may be observed from n trials with success fraction p, at probability P. Note that the value returned is a real-number, and not an integer. Depending on the use case you may want to take either the floor or ceiling of the result. For example:  
> `quantile(complement(binomial(n, p), P))`  
  
Explanation:   
`quantile` searches for equal probability values over the PDF and maps to a number of success. Binomial distribution has a PDF close to a bell shape, thus given a probability value, there will be two solutions: to the left of the peak (smaller) and to the right of the peak (larger).  
`quantile(binomial(n, p), P)` always computes the first (lower) value (starting from the left) and `quantile(complement(binomial(n, p), P))` computes the second (higher) value (complement starts on the right), logically and practically.  
  
Here is an example code:  
```  
#include <boost/math/distributions/binomial.hpp>  
int main() {  
using namespace boost::math;  
constexpr auto N = 100u;  
auto dist = binomial_distribution<double>(N, 0.5); // bell shaped with a peak in the middle  
auto prob = pdf(dist, 1.0*N/3); // pick a probability at 1/3, there should be 2/3 mirror image  
std::cout << quantile(dist, prob) << '\n';             // prints 32  
std::cout << quantile(complement(dist, prob)) << '\n'; // prints 67  
}  
```  
Also, I am a bit confused about those remarks about real-number return value: it seems that it is already rounded.

---

## Comment 1

> Username: pabristow  
> Created at: 2019-05-09 11:59:50 UTC  
> Url: https://github.com/boostorg/math/issues/203#issuecomment-490873877  

I fear that you are right :-(  
  
I will try to improve for the next release, and perhaps add your example.  
  
The warning is that (unlike some implementations that explicitly return an integer as required by mathematics) computational noise might have unexpected results when converting the real result to an integer.  In practice, rounding will very probably give the expected integer value.

---

## Comment 2

> Username: mikucionisaau  
> Created at: 2019-05-09 17:30:09 UTC  
> Url: https://github.com/boostorg/math/issues/203#issuecomment-490994937  

I am having a second look at this and it seems to be a language issue, i.e. it really depends on the goal (the null hypothesis), but it is not stated, perhaps it was written with something particular in mind, I imagine the names could be reversed for a particular purpose.  
  
Now I am having precision issues with the quantile function (at least), there is really something strange with rounding: the binomial distribution is discrete and it can be computed precisely (in principle), so there should be no need for rounding. The `pdf` seems to have very good precision: everything adds up.  
Here is an example of computing p-value for binomial test:  
https://coliru.stacked-crooked.com/a/4488121e4effd621  
  
I am using `long double`, because I noticed that `double` is only good for up to `60` trials (binomial coeficients overflow the number of digits passed that value).  
  
The first function is simple naive implementation-by-definition and the values seem correct, in the table at the bottom:  
given the binomial distribution of `true_prob==0.5`, the 50 out 100 sample gives `pvalue==1`, i.e. this is the most typical sample and hence no surprizes at all. Other samples are rarer and the p-value is lower accordingly. One issue: the values are symmetric when `double` is used and they are not when `long double` is chosen, which is supposed to provide better precision.  
  
The second implementation tries to achieve the same thing but instead of scanning the whole range it uses `quantile` to find the lower and upper bounds and then sum up the ranges. The issue is that it finds quantiles which are further apart (`lower` and `upper`):  
given the 50 successes out 100 trials the quantiles are 43 and 57 (not 50!), and then the cdf does not add up to `1`. The example of 50 out 100 is not important, rarer case are. Interestingly the values are always symmetric, some of the values coincide with naive implementation.  
  
It's bizare and I do not know which implementation to choose.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-05-09 18:35:48 UTC  
> Url: https://github.com/boostorg/math/issues/203#issuecomment-491017936  

Given:  
  
```  
   boost::math::binomial_distribution<> d(100, 0.5);  
   std::cout << quantile(d, 0.5) << std::endl;  
   std::cout << quantile(complement(d, 0.5)) << std::endl;  
```  
  
I see `50` output for both as expected, can you please give a specific example where the quantile function doesn't give the value you expect?  
  
Note the paragraph in the docs which says:  
  
" _The quantile function will by default return an integer result that has been rounded outwards. That is to say lower quantiles (where the probability is less than 0.5) are rounded downward, and upper quantiles (where the probability is greater than 0.5) are rounded upwards. This behaviour ensures that if an X% quantile is requested, then at least the requested coverage will be present in the central region, and no more than the requested coverage will be present in the tails._  
  
_This behaviour can be changed so that the quantile functions are rounded differently, or even return a real-valued result using Policies. It is strongly recommended that you read the tutorial Understanding Quantiles of Discrete Distributions before using the quantile function on the Binomial distribution. The reference docs describe how to change the rounding policy for these distributions._ "

---

## Comment 4

> Username: mikucionisaau  
> Created at: 2019-05-10 06:35:47 UTC  
> Url: https://github.com/boostorg/math/issues/203#issuecomment-491174210  

Ouch, that was my mistake: the quantiles work on the area under PDF (i.e. CDF) and not the PDF values, hence my treatment of `quantile` is wrong and the second implementation wrong. I misunderstood the concept, sorry.   
  
If you are going to use the example, please do not use the line `auto prob = pdf(dist, 1.0*N/3);` -- that's not a good combination.

---

## Comment 5

> Username: mikucionisaau  
> Created at: 2019-05-10 06:58:46 UTC  
> Updated at: 2019-05-10 07:02:42 UTC  
> Url: https://github.com/boostorg/math/issues/203#issuecomment-491179307  

Also, if one uses probabilities greater than 0.5 then the `quantile` return the values greater than the `quantile` of the `complement` as the current documentation describes.   
  
```cpp  
auto dist = binomial_distribution<Prob>(100, 0.5);  
std::cout << quantile(dist, 0.9) << ' ' << quantile(complement(dist, 0.9)) << std::endl;  
```  
Prints "56 43"  
  
It depends on what one is looking for, I was looking for tails.  
Perhaps a more meaningful example would be to find an interval in the middle where 90% values fall by cutting the 5% tails from both ends:  
```cpp  
auto dist = binomial_distribution<Prob>(100, 0.5);  
std::cout << quantile(dist, 0.05) << ' ' << quantile(complement(dist, 0.05)) << std::endl;  
```  
Prints "41 58".

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-05-10 17:10:45 UTC  
> Url: https://github.com/boostorg/math/issues/203#issuecomment-491362510  

To get back to the original issue, the wording should be something more like:  
  
quantile: Given a binomial distribution with n trials, success fraction p and probability P, finds the largest number of successes k whose CDF is less than P.  
quantile complement: Given a binomial distribution with n trials, success fraction p and probability Q, finds the smallest number of successes k whose CDF is greater than 1-Q.

---

## Comment 7

> Username: mikucionisaau  
> Created at: 2019-05-10 18:18:20 UTC  
> Url: https://github.com/boostorg/math/issues/203#issuecomment-491384419  

I agree with the @jzmaddock's formulation. Also an immediate link to the [tutorial](https://www.boost.org/doc/libs/1_70_0/libs/math/doc/html/math_toolkit/pol_tutorial/understand_dis_quant.html) would be nice, because it explains everything in a visual way, espcially about being extra careful about the complement: the distribution is discrete and different number of successes can be picked up. It is a similar issue to precision, but more subtle.
