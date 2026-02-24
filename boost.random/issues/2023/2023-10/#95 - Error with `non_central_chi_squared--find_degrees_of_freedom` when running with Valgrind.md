# #95 - Error with `non_central_chi_squared::find_degrees_of_freedom` when running with Valgrind [Open]

> Username: stla  
> Created at: 2023-10-13 19:47:42 UTC  
> Updated at: 2023-10-13 21:01:28 UTC  
> Url: https://github.com/boostorg/random/issues/95  

Hello,  
  
I ported the function `non_central_chi_squared::find_degrees_of_freedom` to a R package. My R-C++ function is just:  
  
```cpp  
double find_chisq_df(double delta, double q, double p){  
  // gives nu such that pchisq(q, nu, delta) = p  
  return non_central_chi_squared::find_degrees_of_freedom(delta, q, p);  
}  
```  
  
When an example is executed under Valgrind, this error is generated:  
  
```  
Error in function boost::math::non_central_chi_squared_distribution<long double>::non_central_chi_squared_distribution(long double,long double): Degrees of freedom argument is -nan, but must be > 0 !  
```  
  
Would you have an idea of the cause? I'm really not fluent in C++.
