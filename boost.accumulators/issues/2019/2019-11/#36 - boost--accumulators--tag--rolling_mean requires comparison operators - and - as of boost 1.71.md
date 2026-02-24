# #36 - [Regression] boost::accumulators::tag::rolling_mean requires comparison operators < and > as of boost 1.71. [Open]

> Username: thebrandre  
> Created at: 2019-11-11 17:45:06 UTC  
> Updated at: 2019-11-12 09:09:59 UTC  
> Url: https://github.com/boostorg/accumulators/issues/36  

Code that uses boost::accumulators::accumulator_set with<X, Stats> where Stats contains boost::accumulators::tag::rolling_mean and X does not implement the operators < and > no longer compiles since [Fix #11437: correct immediate_rolling_mean](https://github.com/boostorg/accumulators/commit/5ff527ec3b30cba849f6f69067b8956b27ed73bd).  
  
We were using this for coordinates in 3d, let's say `struct Vec3d { float x; float y; float z};` . This type doesn't have a natural order. (Note that lexicographic ordering does not respect rotation symmetries, etc.) So it doesn't make sense to define comparison operators for them.  
Averaging, by contrast, is basically a linear combination, which is a perfectly valid operation for elements of vector spaces.   
  
Code sample on [godbolt](https://godbolt.org/z/BYWpGg). This code breaks when upgrading from boost-1.70 to boost -1.71.
