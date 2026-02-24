# #343 - [kernel-generation] Investigate alternatives for types for kernel generation [Open]

> Username: simmplecoder  
> Created at: 2019-07-21 21:19:36 UTC  
> Updated at: 2019-07-25 15:29:51 UTC  
> Url: https://github.com/boostorg/gil/issues/343  

At the moment, simple kernels (normalized and unnormalized mean, Gaussian blur) use `boost::gil::gray32f_view_t`, but that might not be the only, or best, solution.  
  
### References  
  
- https://lists.boost.org/boost-gil/2019/07/0273.php  
- @mloskot's suggested in https://github.com/boostorg/gil/pull/342#pullrequestreview-264528946 to make it compatible with the existing `kernel_2d` types
