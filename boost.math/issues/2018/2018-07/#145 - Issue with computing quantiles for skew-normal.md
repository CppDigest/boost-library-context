# #145 - Issue with computing quantiles for skew-normal [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 18:04:16 UTC  
> Updated at: 2018-08-19 17:57:17 UTC  
> Closed at: 2018-08-19 17:57:17 UTC  
> Url: https://github.com/boostorg/math/issues/145  

The 0.075 skew-normal quantile breaks (returns an inf) with certain, seemingly reasonable parameters. Other surrounding values are okay. The complemented version works for some reason.  
  
Here's some test code  
```  
#include <iostream>  
#include <boost/math/distributions.hpp>   
  
int main()  
{  
    boost::math::skew_normal_distribution<> dist(2.0, 1.0, -2.5);  
  
    std::vector<double> qs = { 0.05, 0.075, 0.1 };  
    for(auto q : qs) {  
        std::cout << "q : " << q << std::endl;  
        std::cout << "w/o complement: " << quantile(dist, q) << std::endl;  
        std::cout << "w complement: " << quantile(complement(dist, 1 - q)) << std::endl << std::endl;  
    }  
}  
```  
Dunno how easy this is to fix. I can work around it where I'm using it, but it seems like something that could be fixed.  
  
Thanks!  
  
Moved from Trac.
