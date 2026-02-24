# #21 - Add offline program cache support [Closed]

> Username: kylelutz  
> Created at: 2013-12-21 19:30:44 UTC  
> Updated at: 2014-01-07 07:10:08 UTC  
> Closed at: 2014-01-07 07:10:08 UTC  
> Url: https://github.com/boostorg/compute/issues/21  

Add support for caching program binaries offline. NVIDIA's OpenCL implementation already does this automatically (it stores binaries in ~/.nv/ComputeCache) but this would be a big win for other implementations (e.g. AMD, Intel).

---

## Comment 1

> Username: ddemidov  
> Created at: 2013-12-26 11:23:43 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31218458  

Hi Kyle, have a look at 3938764. There are still a couple of problems.  
  
Namely, I [add device and platform information](https://github.com/ddemidov/compute/blob/3938764f227d47ff421cdf5d564ec32dd9d15347/include/boost/compute/detail/meta_kernel.hpp#L336) to the program source in order to make the cache key differ for different devices/platforms. May be doing this inside `program::create_from_sources` would be a better idea, as it would cover user-supplied kernels.  
  
Also I am not fond of how Boost system and filesystem components are handled [here](https://github.com/kylelutz/compute/blob/3938764f227d47ff421cdf5d564ec32dd9d15347/perf/CMakeLists.txt#L3-L14). It will not work if `BOOST_COMPUTE_TIMER_USE_STD_CHRONO` is defined (there would a missing references to Boost.system).

---

## Comment 2

> Username: kylelutz  
> Created at: 2013-12-26 20:04:53 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31233744  

Awesome, thanks Denis!  
  
I'm out travelling on vacation right now but I'll test it out when I get back next week.  
  
`BOOST_COMPUTE_TIMER_USE_STD_CHRONO` should probably just go away. I think I remember adding it due to lacking C++11 support in gcc-4.6. I'll look into it.  
  
-kyle

---

## Comment 3

> Username: ddemidov  
> Created at: 2013-12-27 06:20:34 UTC  
> Updated at: 2013-12-27 06:40:30 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31248811  

The radix_sort test fails because the radix sort algorithm uses compile options in order to supply type definitions. The compile options are not available in `program::create_with_source()`, and hence do not participate in hashing. This results in wrong kernel binary being loaded.  
  
I don't see how this problem could be solved (in a non-ugly way) without merging the functionality of `create_with_source()` and `build()`.

---

## Comment 4

> Username: ddemidov  
> Created at: 2013-12-27 06:58:42 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31249582  

Some performance results. Total time reported by `make test`:  
  
| Platform | Compute device | empty cache | filled cache |  
| --- | --- | --- | --- |  
| AMD | Capeverde GPU | 36.68 sec | 23.11 sec |  
| AMD | Intel Core i7 | 35.10 sec | 21.44 sec |  
| Intel | Intel Core i7 | 51.35 sec | 50.17 sec |  
| NVIDIA | Tesla K20c | 31.44 sec | 31.26 sec |  
  
These are more or less consistent with what I observe with VexCL caching.

---

## Comment 5

> Username: kylelutz  
> Created at: 2013-12-28 17:03:33 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31300279  

Nice! Looks like a good improvement on AMD. I'll test it out next week.

---

## Comment 6

> Username: kylelutz  
> Created at: 2014-01-06 00:21:09 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31620417  

So I've looked over it a bit and it looks great!  
  
One issue I see is that we should be storing more information about the program to ensure that the cached file is valid. Namely the program options supplied to `program::build()` along with version information about the OpenCL implementation used to compile the program. The latter would be important if the user updates their OpenCL drivers after a program has been cached in which case the program should be re-built.  
  
Thoughts?

---

## Comment 7

> Username: ddemidov  
> Created at: 2014-01-06 06:12:50 UTC  
> Updated at: 2014-01-06 08:00:12 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31629565  

I append [comment block containing the platform and device info](https://github.com/ddemidov/compute/blob/offline-cache/include/boost/compute/program.hpp#L292-L298) to the source. This alters the SHA1 fingerprint of the program and is enough to distinguish between kernels compiled for different platforms. In VexCL I also have [compile options](https://github.com/ddemidov/vexcl/blob/master/vexcl/backend/opencl/compiler.hpp#L130-L144) there (and host compiler version: I had some segfaults while using OpenCL binaries created from another host compiler). Adding OpenCL platform version to the comment block should be easy.  
  
Handling the program options in Boost.Compute seems problematic. In VexCL I have `build_sources()` function which both creates the program and  builds it. So the offline cache handling is naturally embedded there. The functionality is split between `program::create_with_source()` and `program::build()` in Boost.Compute. The source is passed to `create_with_source()`, and the program options are only available in the `build()` function.  
  
I could move the cache handling to the `build()` function, but the function also handles the cases when the program was created with binaries. How about adding `build_from _source()` and `build_from_binary()` functions, and making `build()` dispatch between those?  
  
The drawback of this approach is that the `create_with_source()` would either just store the program source without creating the actual program, or `build_from_source()` would later re-create the program (because it would alter the program source). So one could not rely on `program::get()` to return consistent program id any more.

---

## Comment 8

> Username: ddemidov  
> Created at: 2014-01-06 09:03:10 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31634986  

Kyle,  
  
I know you want to stick to the standard API as close as possible, but how about introducing `program::build_with_source()` function that would have the combined functionality of `program::create_with_source()` and `program::build()`? It would allow for a natural implementation of offline cache; you could use it for all internal needs; and you could still provide separate `program::create_with_source()` and `program::build()` functions in case one needs those.

---

## Comment 9

> Username: kylelutz  
> Created at: 2014-01-06 16:04:42 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31659733  

I like it. The main place to use it would probably be [meta_kernel.hpp:L346](https://github.com/kylelutz/compute/blob/master/include/boost/compute/detail/meta_kernel.hpp#L346 meta_kernel).  
  
Thanks for working on this!  
  
-kyle

---

## Comment 10

> Username: kylelutz  
> Created at: 2014-01-07 07:10:08 UTC  
> Url: https://github.com/boostorg/compute/issues/21#issuecomment-31717504  

Implemented in PR #39
