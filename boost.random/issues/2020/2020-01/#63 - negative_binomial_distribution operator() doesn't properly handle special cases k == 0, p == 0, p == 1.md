# #63 - negative_binomial_distribution operator() doesn't properly handle special cases k == 0, p == 0, p == 1 [Open]

> Username: tc3t  
> Created at: 2020-01-14 20:24:30 UTC  
> Updated at: 2020-01-14 20:33:22 UTC  
> Url: https://github.com/boostorg/random/issues/63  

Currently negative_binomial_distribution constructor [requires](https://github.com/boostorg/random/blob/8c30753bf49504b5f677b8b410a3787907c86bd0/include/boost/random/negative_binomial_distribution.hpp#L97) k >=0 && 0 <= p <= 1, but [operator()](https://github.com/boostorg/random/blob/8c30753bf49504b5f677b8b410a3787907c86bd0/include/boost/random/negative_binomial_distribution.hpp#L117) is implemented as follows:  
```  
template<class URNG>  
IntType operator()(URNG& urng) const  
{  
    gamma_distribution<RealType> gamma(_k, (1-_p)/_p);  
    poisson_distribution<IntType, RealType> poisson(gamma(urng));  
    return poisson(urng);  
}  
```  
Since gamma_distribution constructor [requires](https://github.com/boostorg/random/blob/21d66a0a2cd2864b2c592b6118370b3203d31e31/include/boost/random/gamma_distribution.hpp#L111) both input parameters to be > 0, the code violates preconditions when having _k == 0 or _p == 1; also case _p == 0 leads to invalid results.  
  
- Case p == 1: Would seem easy to fix by adding `if (_p == 1) return 0;` given the trivial distribution.  
- Case p == 0:  is almost redundant and for example C++ standard [1] and many other definitions [2] [3] require p > 0: p can be zero only if k == 0, otherwise the distribution is not well defined (P(i|k,0) is zero for all i >= 0 when k > 0).  
- Case k == 0: for example C++ standard [1] and many other definitions [2] [3] require k > 0.  
  
**Example program**  
```  
#include <random>  
#include <boost/random/negative_binomial_distribution.hpp>  
  
int main()  
{  
    std::mt19937 randEng;  
    boost::random::negative_binomial_distribution<int>(0, 0.5)(randEng); // BOOST_ASSERT() fails in gamma_distribution constructor  
    boost::random::negative_binomial_distribution<int>(10, 0)(randEng); // Returns bogus (gamma_distribution gets inf as second parameter)  
    boost::random::negative_binomial_distribution<int>(10, 1)(randEng); // BOOST_ASSERT() fails in gamma_distribution constructor  
    boost::random::negative_binomial_distribution<int>(0, 0)(randEng); // BOOST_ASSERT() fails in gamma_distribution constructor  
    return 0;  
}  
```  
  
  
[1]: Checked from draft N4842 (2019-11-27)  
[2] https://se.mathworks.com/help/stats/prob.negativebinomialdistribution.html  
[3] http://search.r-project.org/R/library/stats/html/NegBinomial.html
