# #265 Linear regression [Merged]

> Username: NAThompson  
> Created at: 2019-10-20 14:01:59 UTC  
> Updated at: 2019-10-27 16:30:26 UTC  
> Merged at: 2019-10-27 16:30:22 UTC  
> Closed at: 2019-10-27 16:30:22 UTC  
> Url: https://github.com/boostorg/math/pull/265  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-10-21 13:28:22 UTC  
> Url: https://github.com/boostorg/math/pull/265#issuecomment-544514459  

Always annoying when I think up another test which obviates the latest build. But I think the scaling test is in fact very strong.  
  
In any case, if anyone has some time, please take a look. This is more simple than the API provided in Numerical Recipes, Chapter 15, which requires also providing uncertainties on each parameter. I think that NR's choice here is a bit overengineered; my own preference is to keep least squares "dumb", and then use a posteriori tests for homoscedasticity.  
  
Also: I'm looking for a good "robust" linear regression, NR describes one in section 15.7 which minimizes the mean absolute deviation, but there are too many options for me to evaluate them at this point. See [here](https://en.wikipedia.org/wiki/Robust_regression).  
  
In any case, this is clean under sanitizers, unit tested, docs written. I feel like it is too slow, but I have no clue how to speed it up as the time is dominated by the computation of covariance, which *I thought* should be memory bound, but in fact is CPU bound, probably due to bad instructions generated? Dunno.  
  
Here's the benchmark:  
  
```cpp  
#include <benchmark/benchmark.h>  
#include <iostream>  
#include <boost/math/statistics/linear_regression.hpp>  
  
template<typename Real>  
void BMSimpleOrdinaryLeastSquares(benchmark::State& state)  
{  
    using boost::math::statistics::simple_ordinary_least_squares;  
    unsigned n = state.range(0);  
    std::vector<Real> x(n);  
    std::vector<Real> y(n);  
    x[0] = 0;  
    Real c0 = 7.2;  
    Real c1 = 13.6;  
    y[0] = c0;  
    for (size_t i = 1; i < n; ++i) {  
        x[i] = x[i-1] + 0.1;  
	y[i] = c0 + c1*x[i];  
    }  
  
    for (auto _ : state)  
    {  
        auto [c0_computed, c1_computed] = simple_ordinary_least_squares(x,y);  
        benchmark::DoNotOptimize(c0_computed);  
    }  
    state.SetComplexityN(state.range(0));  
    state.SetBytesProcessed(static_cast<int64_t>(state.iterations())*state.range(0)*sizeof(Real));  
  
}  
  
BENCHMARK_TEMPLATE(BMSimpleOrdinaryLeastSquares, float)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(BMSimpleOrdinaryLeastSquares, double)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
  
template<typename Real>  
void BMSimpleOrdinaryLeastSquaresWRSquared(benchmark::State& state)  
{  
    using boost::math::statistics::simple_ordinary_least_squares_with_R_squared;  
    unsigned n = state.range(0);  
    std::vector<Real> x(n);  
    std::vector<Real> y(n);  
    x[0] = 0;  
    Real c0 = 7.2;  
    Real c1 = 13.6;  
    y[0] = c0;  
    for (size_t i = 1; i < n; ++i) {  
        x[i] = x[i-1] + 0.01;  
        y[i] = c0 + c1*x[i];  
    }  
  
    for (auto _ : state)  
    {  
        auto [c0_computed, c1_computed, Rsquared] = simple_ordinary_least_squares_with_R_squared(x,y);  
        benchmark::DoNotOptimize(Rsquared);  
    }  
    state.SetComplexityN(state.range(0));  
    state.SetBytesProcessed(static_cast<int64_t>(state.iterations())*state.range(0)*sizeof(Real));  
  
}  
  
BENCHMARK_TEMPLATE(BMSimpleOrdinaryLeastSquaresWRSquared, float)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(BMSimpleOrdinaryLeastSquaresWRSquared, double)->RangeMultiplier(2)->Range(1 << 3, 1 << 24)->Complexity(benchmark::oN);  
  
BENCHMARK_MAIN();  
  
```

---

## Comment 2

> Username: pabristow  
> Created_at: 2019-10-21 13:46:28 UTC  
> Url: https://github.com/boostorg/math/pull/265#issuecomment-544522262  

For some types of data, I have found least squares decidedly un-'robust', but there is a need for the basic algorithm, and this looks like it.  
  
For other purposes, we can add other various more 'robust' methods later?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-10-21 15:01:40 UTC  
> Url: https://github.com/boostorg/math/pull/265#issuecomment-544556047  

I definitely will be adding the robust linear regression soon, but I'm trying to decide *which* robust linear regression should be added. I actually like NRs, but I haven't seen anything about it's pitfalls. The [Theil-Sen](https://en.wikipedia.org/wiki/Theil%E2%80%93Sen_estimator) estimator looks really good, but naively it's quadratic time, and there are too many different options for making it efficient which I can't evaluate right now.

---
