# #39 Implements offline kernel caching [Merged]

> Username: ddemidov  
> Created at: 2014-01-06 18:57:41 UTC  
> Updated at: 2014-01-07 07:10:08 UTC  
> Merged at: 2014-01-07 06:47:52 UTC  
> Closed at: 2014-01-07 06:47:52 UTC  
> Url: https://github.com/boostorg/compute/pull/39  

See #21  
  
This adds `program::build_with_source()` function that both creates and builds the program for the given context with supplied source and compile options. In case `BOOST_COMPUTE_USE_OFFLINE_CACHE` macro is defined, it also saves the compiled program binary for reuse in the offline cache located in `$HOME/.boost_compute` folder on UNIX-like systems and in `%APPDATA%/boost_compute` folder on Windows.  
  
All internal uses of `program::create_with_source()` followed by `program::build()` are replaced with `program::build_with_source()`.

---

## Comment 1

> Username: ddemidov  
> Created_at: 2014-01-06 19:05:47 UTC  
> Url: https://github.com/boostorg/compute/pull/39#issuecomment-31676352  

All tests except for algorithm.inplace_reduce pass for both clean and filled cache. The algorithm.inplace_reduce test fails on my system on the master branch as well, so I assume the failure was not introduced by this commit. Here is the output of the test (same for the master, develop, and offline-cache branches):  
  
```  
$ ./test_inplace_reduce   
Running 3 test cases...  
set_mempolicy: Function not implemented  
set_mempolicy: Function not implemented  
set_mempolicy: Function not implemented  
/home/demidov/work/opencl/compute/test/test_inplace_reduce.cpp(41): error in "sum_int": check int(vector[0]) == int(36) failed [68 != 36]  
/home/demidov/work/opencl/compute/test/test_inplace_reduce.cpp(63): error in "multiply_int": check int(vector[0]) == int(72900) failed [1771470000 != 72900]  
  
*** 2 failures detected in test suite "TestInplaceReduce"  
```  
  
The system I ran the test on has Intel CPU as the only compute device available (with Intel OpenCL SDK as a platform). My primary test workstation is unfortunately down for the duration of ridiculously long New Year holidays in Russia.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-01-06 20:11:48 UTC  
> Url: https://github.com/boostorg/compute/pull/39#issuecomment-31682446  

I've added second test run for Travis-CI. Clear cache test time is 46.44 sec there, and the second run is 7.66 sec.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-01-06 21:06:17 UTC  
> Url: https://github.com/boostorg/compute/pull/39#issuecomment-31686977  

Looks good, left a few inline comments. And thanks for adding the TravisCI test (know if they're ever going to support testing on Windows?)

---

## Comment 4

> Username: ddemidov  
> Created_at: 2014-01-07 05:18:36 UTC  
> Url: https://github.com/boostorg/compute/pull/39#issuecomment-31714170  

re Travis-CI and Windows: I think they don't have any plans to do that. Windows is extremely hostile to this kind of automation.

---

## Comment 5

> Username: ddemidov  
> Created_at: 2014-01-07 05:19:07 UTC  
> Url: https://github.com/boostorg/compute/pull/39#issuecomment-31714180  

I've reverted Boost version to 1.48 and rewrote the commits.

---

## Comment 6

> Username: ddemidov  
> Created_at: 2014-01-07 05:23:09 UTC  
> Url: https://github.com/boostorg/compute/pull/39#issuecomment-31714304  

Regarding hash collisions for online cache: those are indeed possible, but I don't know how restrictive is sha1 computation. We could try that, or we could save program sources along with the program binaries (and keep the source inside program object instead of relying on OpenCL to do that).  
  
I don't have this problem in VexCL, because each expression has its own type and kernel cache is a static member of that type. The only key I need is context id.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-01-07 06:48:54 UTC  
> Url: https://github.com/boostorg/compute/pull/39#issuecomment-31716849  

Merged! I'll look more into handling possible cache collisions. Also I'll get a fix in for the `inplace_reduce` test failure.  
  
Thanks!

---
