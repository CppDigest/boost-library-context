# #136 - Add prefetch to core [Open]

> Username: HDembinski  
> Created at: 2022-12-23 09:02:43 UTC  
> Updated at: 2022-12-24 08:29:43 UTC  
> Url: https://github.com/boostorg/core/issues/136  

We are currently implementing a new algorithm in Boost.Histogram which is supposed to profit from cacheline prefetching. The prefetch instruction is a builtin, so we need code to get a platform-independent Boost command. I discovered that Boost.Context already has such an implementation in its details.  
  
https://www.boost.org/doc/libs/1_81_0/boost/context/detail/prefetch.hpp  
  
I propose to move this code to Core, so that all libraries can profit from prefetching. Currently, context and fiber use this code, and it is currently added to Boost.Histogram.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-12-23 11:58:25 UTC  
> Url: https://github.com/boostorg/core/issues/136#issuecomment-1363892488  

The code in Boost.Context is specific to x86 (and not entirely correct, BTW - `prefetch_range` should align prefetches to cache line boundaries, otherwise it might not prefetch the last cache line). I think, with recent CPUs explicit prefetching is mostly useless as it doesn't provide tangible benefits compared to the implicit hardware prefetching. Did you actually measure the effect of explicit prefetching with your code?  
  
Also, I'm not sure if explicit prefetching is a thing in other architectures besides x86.

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-12-24 07:59:01 UTC  
> Updated at: 2022-12-24 08:29:43 UTC  
> Url: https://github.com/boostorg/core/issues/136#issuecomment-1364482819  

I did measure for my current project, and I wrote above that it does not accelerate my access pattern on my computer. However, if I had an older CPU with a less smart hardware prefetcher, it might.  
  
The point of this is to give a hint to the CPU which data will be needed next, when you have a more complex access pattern than forward steps with a fixed constant.  
  
Since this is an optimization, it is fine if the implementation evaluates to nothing on platforms that do not provide an intrinsic.  
  
Also, to clarify, I only want the basic prefetch command in core, not prefetch_range.
