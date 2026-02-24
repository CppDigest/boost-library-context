# #260 Runs test [CI SKIP] [Merged]

> Username: NAThompson  
> Created at: 2019-10-13 14:02:42 UTC  
> Updated at: 2019-10-15 12:39:30 UTC  
> Merged at: 2019-10-15 12:39:27 UTC  
> Closed at: 2019-10-15 12:39:27 UTC  
> Url: https://github.com/boostorg/math/pull/260  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-10-13 23:43:03 UTC  
> Url: https://github.com/boostorg/math/pull/260#issuecomment-541471008  

Posting this performance benchmark just as an easy way to get it from box to box:  
  
```cpp  
#include <benchmark/benchmark.h>  
#include <random>  
#include <iostream>  
#include <algorithm>  
#include <boost/math/statistics/runs_test.hpp>  
  
  
  
template<typename Real>  
void BMRunsAboveAndBelowMedian(benchmark::State& state)  
{  
    using boost::math::statistics::runs_above_and_below_median;  
    unsigned n = state.range(0);  
    std::vector<Real> v(n);  
    std::random_device rd;  
    std::normal_distribution<Real> dis(0,1);  
    for (size_t i = 0; i < n; ++i) {  
        v[i] = dis(rd);  
    }  
  
    for (auto _ : state)  
    {  
        auto [t, p] = runs_above_and_below_median(v);  
        benchmark::DoNotOptimize(t);  
    }  
    state.SetComplexityN(state.range(0));  
    state.SetBytesProcessed(static_cast<int64_t>(state.iterations())*state.range(0)*sizeof(Real));  
  
}  
  
BENCHMARK_TEMPLATE(BMRunsAboveAndBelowMedian, float)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(BMRunsAboveAndBelowMedian, double)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
  
  
template<typename Real>  
void BMRunsAboveAndBelowThreshold(benchmark::State& state)  
{  
    using boost::math::statistics::runs_above_and_below_threshold;  
    unsigned n = state.range(0);  
    std::vector<Real> v(n);  
    std::random_device rd;  
    std::normal_distribution<Real> dis(0,1);  
    for (size_t i = 0; i < n; ++i) {  
        v[i] = dis(rd);  
    }  
  
    for (auto _ : state)  
    {  
        auto [t, p] = runs_above_and_below_threshold(v, Real(0));  
        benchmark::DoNotOptimize(t);  
    }  
    state.SetComplexityN(state.range(0));  
    state.SetBytesProcessed(static_cast<int64_t>(state.iterations())*state.range(0)*sizeof(Real));  
}  
  
BENCHMARK_TEMPLATE(BMRunsAboveAndBelowThreshold, float)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(BMRunsAboveAndBelowThreshold, double)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
  
BENCHMARK_MAIN();  
```

---
