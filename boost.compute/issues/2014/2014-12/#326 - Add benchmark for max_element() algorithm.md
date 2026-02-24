# #326 - Add benchmark for max_element() algorithm [Closed]

> Username: kylelutz  
> Created at: 2014-12-03 06:03:15 UTC  
> Updated at: 2014-12-11 06:28:42 UTC  
> Closed at: 2014-12-11 06:28:42 UTC  
> Url: https://github.com/boostorg/compute/issues/326  

Add a benchmark for the [`max_element()`](http://kylelutz.github.io/compute/boost/compute/max_element.html) algorithm (i.e. `perf_max_element`).  
  
See the [`perf/perf_count.cpp`](https://github.com/kylelutz/compute/blob/master/perf/perf_count.cpp) benchmark for an example/template.  
  
It would also be good to add a corresponding benchmark for the STL `std::max_element()` algorithm (i.e. `perf_stl_max_element`).

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-11 06:28:42 UTC  
> Url: https://github.com/boostorg/compute/issues/326#issuecomment-66578606  

Implemented in PR #337.
