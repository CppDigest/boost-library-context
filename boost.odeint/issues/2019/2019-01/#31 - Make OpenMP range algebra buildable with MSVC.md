# #31 - Make OpenMP range algebra buildable with MSVC [Open]

> Username: MadCatX  
> Created at: 2019-01-12 18:58:48 UTC  
> Updated at: 2019-01-12 18:58:48 UTC  
> Url: https://github.com/boostorg/odeint/issues/31  

Current implementation of openmp_range_algebra uses unsigned type (size_t) as index in for loops that are parallelized by OpenMP. This is not supported by MSVC. Additionally, as MSVC is still stuck with OpenMP 2.0 support, the OpenMP reduction syntax used by `norm_inf` also does not compile. The following patch fixes the issue; at least of MSVC 14.  
[0001-Make-OpenMP-range-algebra-buildable-with-MSVC.txt](https://github.com/boostorg/odeint/files/2752406/0001-Make-OpenMP-range-algebra-buildable-with-MSVC.txt)
