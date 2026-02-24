# #81 make move constructor and operator= noexcept [Merged]

> Username: lakshayg  
> Created at: 2017-08-20 16:57:30 UTC  
> Updated at: 2017-08-23 18:02:50 UTC  
> Merged at: 2017-08-23 18:02:50 UTC  
> Closed at: 2017-08-23 18:02:50 UTC  
> Url: https://github.com/boostorg/math/pull/81  

This change helps the polynomial class play well with STL  
containers.  
  
> If the move constructor for an element type in a container  
> is not noexcept then the container will use the copy constructor rather  
> than the move constructor -- HIC++ Version 4.0  
  
Benchmarking shows that the number of calls to copy constructor  
are reduced.  
  
Here is the benchmarking code I used:  
  
```cpp  
#include "benchmark/benchmark.h"  
#include "boost/math/tools/polynomial.hpp"  
#include <vector>  
  
using namespace boost::math::tools;  
  
auto get_polynomial(size_t len, double val) {  
	std::vector<double> data(len, val);  
	return polynomial<double>(data.begin(), data.end());  
}  
  
static void stlContainers(benchmark::State& state) {  
  int N = state.range(0);  
  while (state.KeepRunning()) {  
    std::vector<polynomial<double> > vec;  
    for (int i=0; i<10000; ++i)  
      vec.push_back(get_polynomial(N, 1.2));  
  }  
}  
BENCHMARK(stlContainers)  
  ->RangeMultiplier(8)  
  ->Range(1, 4096);  
  
BENCHMARK_MAIN();  
```

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-08-20 18:05:47 UTC  
> Url: https://github.com/boostorg/math/pull/81#issuecomment-323601365  

Yup, thanks for this, I have a bunch of changes pending as well that remove the odd stray argument copy here and there, as well as modernizing the code a little here and there.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-08-20 18:16:14 UTC  
> Url: https://github.com/boostorg/math/pull/81#issuecomment-323601985  

Those should of course use BOOST_NOEXCEPT rather than noexcept directly: for C++03.

---

## Comment 3

> Username: lakshayg  
> Created_at: 2017-08-21 02:24:23 UTC  
> Url: https://github.com/boostorg/math/pull/81#issuecomment-323631869  

@jzmaddock Done! thanks.

---
