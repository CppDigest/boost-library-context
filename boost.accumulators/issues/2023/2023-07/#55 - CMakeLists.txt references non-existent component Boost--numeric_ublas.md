# #55 - CMakeLists.txt references non-existent component Boost::numeric_ublas [Open]

> Username: jimlloyd-mse  
> Created at: 2023-07-17 20:15:18 UTC  
> Updated at: 2023-09-25 17:34:10 UTC  
> Url: https://github.com/boostorg/accumulators/issues/55  

I do not know the history here, but it seems that the component numeric_ublas was probably recently removed but CMakeLists.txt was not updated.  
  
I have found that simply removing the line containing "Boost::numeric_ublas" is sufficient to build `boost-1.82.0`.

---

## Comment 1

> Username: jdumas  
> Created at: 2023-09-25 17:34:10 UTC  
> Url: https://github.com/boostorg/accumulators/issues/55#issuecomment-1734186686  

Ublas is still a required dependency, e.g. see [this header](https://github.com/boostorg/accumulators/blob/9d9e5dae2202660f57e2dc91efb620aa001525b3/include/boost/accumulators/statistics/covariance.hpp#L21-L22). The real issue is that Ublas is the only Boost library that is missing CMake support at the moment. See [this PR](https://github.com/boostorg/ublas/pull/142) for more details.
