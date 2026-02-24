# #42 - Nans in quantile accumulators [Open]

> Username: wf34  
> Created at: 2020-04-21 12:05:05 UTC  
> Updated at: 2024-04-16 11:56:36 UTC  
> Url: https://github.com/boostorg/accumulators/issues/42  

This code is a slightly modified example from the Boost.Accumualators documentation, https://scicomp.ethz.ch/public/manual/Boost/1.55.0/accumulators.pdf , p.50.  
  
It has two constants, `n`, `c` : sample size and cache size.  
The purpose of this library, if I'm not mistaken, that the stats are computed for `c << n`.  
However, this code yields nans and fails the test.  
When the cache size is set equal to a sample size `c = n;` the test passes.  
  
```  
#include <iostream>  
  
#include <boost/accumulators/accumulators.hpp>   
#include <boost/accumulators/statistics/stats.hpp>  
#include <boost/accumulators/statistics/tail_quantile.hpp>  
#include <boost/random/lagged_fibonacci.hpp>  
#include <cassert>  
  
using namespace boost::accumulators;  
  
int main() {  
  double epsilon = 1.e-2;  
  std::size_t n = 100000; // number of MC steps  
  std::size_t c = 10000; // cache size  
  typedef accumulator_set<double, stats<tag::tail_quantile<right> > > accumulator_t_right;  
  typedef accumulator_set<double, stats<tag::tail_quantile<left> > > accumulator_t_left;  
  accumulator_t_right acc0( tag::tail<right>::cache_size = c );  
  accumulator_t_left acc2( tag::tail<left>::cache_size = c );  
  
  boost::lagged_fibonacci607 rng;  
  
  for (std::size_t i = 0; i < n; ++i) {  
    double sample1 = rng();  
    acc0(sample1);  
    acc2(sample1);  
  }  
  
  std::cout << quantile(acc0, quantile_probability = 0.95) << std::endl;  
  std::cout << quantile(acc0, quantile_probability = 0.75) << std::endl;  
  std::cout << quantile(acc2, quantile_probability = 0.25) << std::endl;  
  std::cout << quantile(acc2, quantile_probability = 0.05) << std::endl;  
  
  assert(std::fabs(quantile(acc0, quantile_probability = 0.95 )- 0.95) < epsilon);  
  assert(std::fabs(quantile(acc0, quantile_probability = 0.975)- 0.975) <epsilon );  
  assert(std::fabs(quantile(acc0, quantile_probability = 0.75)- 0.75) <epsilon );  
  assert(std::fabs(quantile(acc0, quantile_probability = 0.99 )- 0.99) <epsilon );  
  assert(std::fabs(quantile(acc0, quantile_probability = 0.999)- 0.999) <epsilon );  
  assert(std::fabs(quantile(acc0, quantile_probability = 0.25)- 0.25) <epsilon );  
  assert(std::fabs(quantile(acc2, quantile_probability = 0.05 )- 0.05) <epsilon );  
  assert(std::fabs(quantile(acc2, quantile_probability = 0.025)- 0.025) <epsilon );  
  assert(std::fabs(quantile(acc2, quantile_probability = 0.01 )- 0.01) <epsilon );  
  assert(std::fabs(quantile(acc2, quantile_probability = 0.001)- 0.001)<epsilon );  
  
  return 0;  
}  
```  
The output:  
```  
0.949523  
nan  
nan  
0.0516118  
a.out: ./1.cpp:35: int main(): Assertion `std::fabs(quantile(acc0, quantile_probability = 0.75)- 0.75) <epsilon' failed.  
```

---

## Comment 1

> Username: adimajo  
> Created at: 2024-04-16 11:56:35 UTC  
> Url: https://github.com/boostorg/accumulators/issues/42#issuecomment-2058915452  

You cache 10000 samples among 100000, so if my understanding is correct, you cannot estimate past the 90th quantile (resp. before the 10th quantile) by design, see [`tail_quantile.hpp`](https://github.com/boostorg/accumulators/blob/develop/include/boost/accumulators/statistics/tail_quantile.hpp#L82).
