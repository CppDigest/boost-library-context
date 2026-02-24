# #800 - non_central_t distribution reports kurtosis + 3 [Closed]

> Username: mckib2  
> Created at: 2022-07-14 16:44:54 UTC  
> Updated at: 2022-08-05 08:08:09 UTC  
> Closed at: 2022-08-05 08:08:09 UTC  
> Url: https://github.com/boostorg/math/issues/800  

It appears to us that `boost::math::non_central_t` is using the formula for (not-excess/Pearson) kurtosis as the implementation for `kurtosis_excess`, leading to an extra +3 for both `kurtosis` and `kurtosis_excess`.  Here is a simple program demonstrating that using reference values from wolfram alpha [ExcessKurtosis[NoncentralStudentTDistribution[10, 3]]](https://www.wolframalpha.com/input?i2d=true&i=ExcessKurtosis%5C%2891%29NoncentralStudentTDistribution%5C%2891%2910%5C%2844%29+3%5C%2893%29%5C%2893%29) and [Kurtosis[NoncentralStudentTDistribution[10, 3]]](https://www.wolframalpha.com/input?i2d=true&i=Kurtosis%5C%2891%29NoncentralStudentTDistribution%5C%2891%2910%5C%2844%29+3%5C%2893%29%5C%2893%29):  
  
```cpp  
#include <iostream>  
#include "boost/math/distributions/non_central_t.hpp"  
  
int main() {  
  constexpr double v = 10;  
  constexpr double delta = 3;  
  boost::math::non_central_t_distribution<double> nct(v, delta);  
  std::cout << "       kurtosis is " << boost::math::kurtosis(nct) << " (expected 5.44)" << std::endl;  
  std::cout << "excess kurtosis is " << boost::math::kurtosis_excess(nct) << " (expected 2.44)" << std::endl;  
  return 0;  
}  
```  
  
Output is:  
```  
       kurtosis is 8.44235 (expected 5.44)  
excess kurtosis is 5.44235 (expected 2.44)  
```  
  
Simply subtracting 3 appears to resolve the issue. xref https://github.com/scipy/scipy/pull/16591

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-07-14 16:47:36 UTC  
> Url: https://github.com/boostorg/math/issues/800#issuecomment-1184666239  

Will investigate.
