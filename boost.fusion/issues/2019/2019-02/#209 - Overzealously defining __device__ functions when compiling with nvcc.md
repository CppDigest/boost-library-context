# #209 - Overzealously defining __device__ functions when compiling with nvcc [Open]

> Username: BenByington  
> Created at: 2019-02-07 19:08:55 UTC  
> Updated at: 2019-02-12 00:19:04 UTC  
> Url: https://github.com/boostorg/fusion/issues/209  

I'm not entirely sure if this issue belongs here or on the boost umbrella project.  The actual error I'm facing could be argued to originate either in boost::fusion or in boost::accumulators, but the overall strategy boost employs to support cuda is brittle to begin with.  The overarching problem is that portions of boost cannot be used in `.cu` files even if they are not going to be used in actual device code.   This is entirely due to how the sections of boost that support cuda attempt to do so, not due to any intrinsic limitations of host side code in a `.cu` file.    
  
The crux my particular issue is that boost fusion seems to mark many (or all?) functions with `BOOST_FUSION_GPU_ENABLED`, which gets it's value from `BOOST_GPU_ENABLED`, which has the value of `__host__ __device__` when compiling with nvcc (and empty otherwise).  This is problematic because this library is heavily templated, and you cannot call a `__host__` only function from a `__host__ __device__` function.  Boost fusion calls functions it has no control over (injected via templates), and if any of those functions are not decorated with `__host__ __device__` then there will be problems.  Even if the end user code is *only* going to invoke the host side call paths, the mere attempt to define the ill-formed device side call paths causes errors.  
  
A minimal example is included below.  It's a trivial cuda program that doesn't even call any GPU code.  The mere fact that if you compile it with nvcc, boost::fusion will try and define device functions that call host-only boost::accumulator code.  This makes it impossible to use portions of boost code in .cu files, even in portions of the code that will only ever be run on the host.    
  
I can save the following code as "main.cu", and compile with `nvcc` version 10.0.117 on Ubuntu 18.04.  The result is a lot of compilation errors about "identifier is undefined in device code".  If hack things and redefine `BOOST_GPU_ENABLED` to be just `__host__` then there are no compilation issues.    
```  
#include <boost/accumulators/accumulators.hpp>  
#include <boost/accumulators/statistics.hpp>  
  
using features =  
        boost::accumulators::features<  
                boost::accumulators::tag::count,  
                boost::accumulators::tag::mean,  
                boost::accumulators::tag::variance  
        >;  
  
int main() {  
    boost::accumulators::accumulator_set<double, features> accum;  
    return 0;  
}  
```

---

## Comment 1

> Username: Flast  
> Created at: 2019-02-08 00:08:39 UTC  
> Url: https://github.com/boostorg/fusion/issues/209#issuecomment-461643363  

This is not a Fusion specific issue; `BOOST_GPU_ENABLED` is provided by Config. You should open this issue [there](https://github.com/boostorg/config/issues).

---

## Comment 2

> Username: BenByington  
> Created at: 2019-02-08 00:44:14 UTC  
> Url: https://github.com/boostorg/fusion/issues/209#issuecomment-461650419  

I will gladly file an issue there, but I don't agree that there is no Fusion specific issue.  Granted other modules probably do things wrong as well, because the whole strategy is brittle, but that doesn't change the fact that Fusion is doing things incorrectly as well.  
  
Config is correctly determining that we are compiling a `.cu` file using `nvcc` and it is correctly populating `BOOST_GPU_ENABLED` with the necessary information to mark a function is being able to run on both host and gpu.   Everything that Config is trying to do in regards to cuda is correct.  However what Config is *not* doing is saying you need to mark every function as gpu enabled.  
  
Adding `BOOST_FUSION_GPU_ENABLED` (e.g. `__device__ __host__`) to a function isn't a magical panacea to automatically make a function compile for a GPU.  There are a number of things you are not allowed to do from a device function.  Calling a host-only routine is one of those things you are not allowed to do.  If you mark a function as `BOOST_FUSION_GPU_ENABLED` and then proceed to call another function that is only compiled for the host, that is an error.  Just like if you decided to use certain parts of the STL, or certain disallowed c++14 constructs.  Nothing Config is going to do will help if you mark a function as compilable on the GPU, and then proceed to do things that are forbidden in that context.

---

## Comment 3

> Username: Flast  
> Created at: 2019-02-12 00:19:04 UTC  
> Url: https://github.com/boostorg/fusion/issues/209#issuecomment-462551139  

OK, but I don't know which functions are valid or invalid. So, PR is very welcome.
