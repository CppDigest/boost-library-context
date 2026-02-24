# #133 - beta_distribution produces nan values sometimes when given small (close to zero) parameters [Closed]

> Username: Hailios  
> Created at: 2025-06-27 20:40:23 UTC  
> Updated at: 2025-06-30 17:41:40 UTC  
> Closed at: 2025-06-30 17:41:40 UTC  
> Url: https://github.com/boostorg/random/issues/133  

expect to get a value in [0, 1], but sometimes nan is returned.  
  
  
Boost version: 1.87.0, from conan center  
OS: Ubuntu 24.04.2 LTS, kernel 6.8.0-62-generic  
Compiler: g++-14 (Ubuntu 14.2.0-4ubuntu2~24.04) 14.2.0  
CPU: Intel i9-13900K  
  
reproducer (written with gtest):  
```  
#include <gtest/gtest.h>  
#include <boost/random/beta_distribution.hpp>  
#include <random>  
  
using namespace testing;  
TEST(test, reprod) {  
  double beta_param = 0.0020368700639848774;  
  boost::random::beta_distribution<double> dist(beta_param, beta_param);  
  std::mt19937_64 gen(12345);  
  
  for (int i = 0; i < 100; ++i) {  
    double Z = dist(gen);  
    EXPECT_FALSE(std::isnan(Z));  
  }  
}  
```  
when I run the above code I get multiple failures   
```  
reproduce_nan_beta_dist.cpp:13: Failure  
Value of: std::isnan(Z)  
  Actual: true  
Expected: false  
```
