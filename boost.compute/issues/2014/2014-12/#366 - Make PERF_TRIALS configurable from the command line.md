# #366 - Make PERF_TRIALS configurable from the command line [Closed]

> Username: roshanrags  
> Created at: 2014-12-29 18:29:33 UTC  
> Updated at: 2016-06-28 05:09:23 UTC  
> Closed at: 2016-06-28 05:09:21 UTC  
> Url: https://github.com/boostorg/compute/issues/366  

This is left as a TODO comment in `perf.hpp`. I'm putting it up here for better visibility.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-29 18:34:50 UTC  
> Url: https://github.com/boostorg/compute/issues/366#issuecomment-68283965  

Thanks for adding this. Yeah, I'd like to really improve the performance benchmarks to be more flexible and allow different configurations to be run by passing arguments on the command line. Other potential options are to allow testing different data types (e.g. `int`, `short`, `float`, `double`) or changing the characteristics for random input data (e.g. `uniform-distribution`, `sorted`, `reverse-sorted`, `constant`).

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-06-27 16:44:05 UTC  
> Url: https://github.com/boostorg/compute/issues/366#issuecomment-228803024  

It's done, see https://github.com/boostorg/compute/commit/dcddaae8d2bef698ff267ea6e32d90dd9bcf37e5. If you still would like benchmarks to be more flexible, rename this issue or better just open a new one.
