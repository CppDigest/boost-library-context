# #577 - boost non_central_chi_squared_distribution() exception. [Open]

> Username: huapeng01016  
> Created at: 2021-11-04 18:40:43 UTC  
> Updated at: 2021-11-04 19:21:05 UTC  
> Url: https://github.com/boostorg/boost/issues/577  

The following code used to give correct result (1.53) but now (1.77) throws exception:  
  
```  
          boost::math::non_central_chi_squared_distribution<double> nchi2(0.001, 100);  
          double res = quantile(nchi2, 0.00000000000003659);  
  
```  
// use to return 5.8758639130601491, now throws exception  
  
I traced the issue to line 476 in boost/math/distributions/non_central_chi_squared.hpp  
  
instead   
  
```  
               if(guess == 0)  
                  guess = tools::min_value<value_type>();   
```  
add the checking of guess for infinity fixes the issue  
  
```  
               if(guess == 0 || isinf(guess))  
                  guess = tools::min_value<value_type>();   
 ```
