# #168 - Gamma distribution CDF gives NaN for extreme value [Closed]

> Username: nicholasdavies  
> Created at: 2018-12-04 14:03:26 UTC  
> Updated at: 2018-12-07 14:27:21 UTC  
> Closed at: 2018-12-07 09:38:03 UTC  
> Url: https://github.com/boostorg/math/issues/168  

Hello,  
  
For some parameterisations of boost::math::gamma_distribution, the boost::math::cdf accessor fails on large values. Ideally when called with a very large finite (or indeed infinite) value, the cdf for a gamma distribution should evaluate to 1 (almost) regardless of the shape and scale parameters, but it fails for some parameterisations as illustrated by the following code.  
  
```C++  
#include <iostream>  
#include <boost/version.hpp>  
#include <boost/math/distributions/beta.hpp>  
#include <boost/math/distributions/normal.hpp>  
#include <boost/math/distributions/gamma.hpp>  
  
int main()  
{  
    using namespace boost::math;  
  
    std::cout << BOOST_VERSION << "\n";  
  
    // Context: the CDF at the maximum value of a beta distribution evaluates to 1.  
    auto beta_dist = beta_distribution<double>(3, 0.4);  
    double beta_max = range(beta_dist).second;  
    std::cout << "upper bound for Beta(3, 0.4) distribution is " << beta_max << ", cdf at u.b. is " << cdf(beta_dist, beta_max) << "\n";  
  
    // Context: also works for the normal distribution.  
    auto normal_dist = normal_distribution<double>(12, 0.4);  
    double normal_max = range(normal_dist).second;  
    std::cout << "upper bound for Normal(12, 0.4) distribution is " << normal_max << ", cdf at u.b. is " << cdf(normal_dist, normal_max) << "\n";  
  
    // Context: works for the gamma distribution when shape = 1 and scale = 1.  
    auto gamma_dist = gamma_distribution<double>(1, 1);  
    double gamma_max = range(gamma_dist).second;  
    std::cout << "upper bound for Gamma(1, 1) distribution is " << gamma_max << ", cdf at u.b. is " << cdf(gamma_dist, gamma_max) << "\n";  
  
    // MINIMUM EXAMPLE OF BUG IS HERE  
    // Issue: gives NaN for the gamma distribution when shape = 5 and scale = 0.35.  
    auto gamma_dist2 = gamma_distribution<double>(5, 0.35);  
    double gamma_max2 = range(gamma_dist2).second;  
    std::cout << "upper bound for Gamma(5, 0.35) distribution is " << gamma_max2 << ", cdf at u.b. is " << cdf(gamma_dist2, gamma_max2) << "\n";  
  
    return 0;  
}  
```  
  
Output (GCC 8.2.0):  
  
```  
106800  
upper bound for Beta(3, 0.4) distribution is 1, cdf at u.b. is 1  
upper bound for Normal(12, 0.4) distribution is inf, cdf at u.b. is 1  
upper bound for Gamma(1, 1) distribution is 1.79769e+308, cdf at u.b. is 1  
upper bound for Gamma(5, 0.35) distribution is 1.79769e+308, cdf at u.b. is nan  
```  
  
I appreciate that this may seem like an extreme use case, but it would be helpful if the Boost distributions were consistent as illustrated by the "context" uses above. It is sometimes useful to fall back on extreme values of distributions when limits for random variables are not supplied by the user, and in this case it would be handy if the CDF gave the right values.  
  
A related issue is that the documentation states for the gamma distribution that "The domain of the random variable is [0,+∞]"—note the upper bound which includes infinity—but the cdf function throws an exception when called with std::numeric_limits<double>::infinity(), besides failing on finite large values as mentioned above. The normal distribution CDF works fine with infinity, so this was unexpected.  
  
Thanks for a very useful library and best wishes,  
  
Nick  
  
[EDIT: Fixed typo in one of the "Context" code examples above] [EDIT2: typos/clarification]

---

## Comment 1

> Username: pabristow  
> Created at: 2018-12-04 15:15:47 UTC  
> Url: https://github.com/boostorg/math/issues/168#issuecomment-444135935  

I confirm your observation using MSVC, but the cause is not instantly obvious but will receive our attention.  
  
On the vexed question of what to do about infinity.  We started to cater for infinity (starting with the normal distribution) but found that complexity that it introduced was troublesome and worrying and we stopped trying cover this case.  
  
(Aside: you have probably used try'n'catch blocks in your 'real-life' use of Boost.Math, but it is highly recommended to get the carefully crafted error messages.)

---

## Comment 2

> Username: nicholasdavies  
> Created at: 2018-12-04 16:32:03 UTC  
> Updated at: 2018-12-04 16:34:01 UTC  
> Url: https://github.com/boostorg/math/issues/168#issuecomment-444164777  

Thanks for the reply — I originally added a comment here but was mistaken (that is, it fails for 1.78e+308 as well... I thought it didn't, but it's the pdf rather than the cdf that works at that value)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-12-04 19:40:09 UTC  
> Url: https://github.com/boostorg/math/issues/168#issuecomment-444230606  

I need to add an asymptotic approximation for the incomplete gamma and large argument to properly fix this - it's not that hard I just need to check all the relations for upper/lower gammas and write some tests...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-12-07 09:38:29 UTC  
> Url: https://github.com/boostorg/math/issues/168#issuecomment-445176340  

Pending successful CI tests, this should be fixed now.

---

## Comment 5

> Username: nicholasdavies  
> Created at: 2018-12-07 14:27:21 UTC  
> Url: https://github.com/boostorg/math/issues/168#issuecomment-445248522  

Thanks!
