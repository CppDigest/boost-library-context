# #439 - Cache sizes [Closed]

> Username: mborland  
> Created at: 2020-09-28 00:01:14 UTC  
> Updated at: 2020-10-02 14:27:39 UTC  
> Closed at: 2020-10-01 23:05:22 UTC  
> Url: https://github.com/boostorg/math/issues/439  

In the discussion on the prime sieve PR (#400) we have run into the issue several times of not having a cross-platform way of querying the size of the L1 cache. [This repo](https://github.com/mborland/cache) should solve that issue. Is there an existing library in boost that this should go into as a PR? I think it would be useful outside of math, but it is not clear to me where it should go.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-09-28 00:12:49 UTC  
> Url: https://github.com/boostorg/math/issues/439#issuecomment-699707835  

I *suspect*, but do not know, that your syscalls will compile down to a `cpuid` asm instruction. This is one of the slowest instruction on a modern CPU, so it might not pay off to determine the cache size.  
  
Could you godbolt + benchmark your cachesize code?

---

## Comment 2

> Username: mborland  
> Created at: 2020-09-28 02:27:44 UTC  
> Url: https://github.com/boostorg/math/issues/439#issuecomment-699734964  

`sysconf` definitely compiles down to `cpuid`. I added the benchmark (generally copied from constants_performance), and it takes 143ns to run on my machine. My thought was having a default L1D value of 32768, and exposing an interface to change that value.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-09-28 10:15:23 UTC  
> Url: https://github.com/boostorg/math/issues/439#issuecomment-699916641  

Presumably this would only be called once during program initialization anyway - in which case the cost would be negligible?  
  
As for where it belongs.... I have no idea!

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-09-28 14:00:22 UTC  
> Updated at: 2020-09-28 14:12:46 UTC  
> Url: https://github.com/boostorg/math/issues/439#issuecomment-700026024  

What about boost::predef?  
  
I've always thought that the functionality of `cpuid` should be exposed somewhere in boost.

---

## Comment 5

> Username: mborland  
> Created at: 2020-09-28 15:39:49 UTC  
> Url: https://github.com/boostorg/math/issues/439#issuecomment-700105362  

@jzmaddock With the user being responsible for memory allocation with the OI approach this could just be a extra step in initialization. Querying the L1D size should be trivial in comparison to a cache miss/under-utilization.  
  
 @NAThompson I will open an issue with them to see what they say.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2020-10-02 14:27:14 UTC  
> Updated at: 2020-10-02 14:27:39 UTC  
> Url: https://github.com/boostorg/math/issues/439#issuecomment-702765549  

> ...a `cpuid` asm instruction. This is one of the slowest...  
  
On the architecture being discussed, the `cpuid` instruction is one of several instructions with the special characteristic of being a _barrier_ instruction, also known as a _serializing_ instruction. In addition to returning the CPU-ID information in the specified registers, this instruction will block and clear the instruction pipeline. Another such instruction is `rdtsc` on that core.  
  
These instructions are great if you are writing a real-time operating system or similar stuff on the metal. They will, however, hit the performance with the branch and pipeline clear.
