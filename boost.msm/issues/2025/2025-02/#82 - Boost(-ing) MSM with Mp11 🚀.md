# #82 - Boost(-ing) MSM with Mp11 🚀 [Closed]

> Username: chandryan  
> Created at: 2025-02-16 17:53:37 UTC  
> Updated at: 2025-09-08 18:48:52 UTC  
> Closed at: 2025-09-08 18:48:52 UTC  
> Url: https://github.com/boostorg/msm/issues/82  

Hi!  
  
TLDR: I have optimized MSM by leveraging Mp11 and would like to contribute 😄   
  
I've been using MSM for a while and from a perspective of robustness and feature completeness yours is the best out there to this day. I would like to express a big thanks for that ❤   
  
I've been struggling with a lot of RAM usage and high compilation times as is known from MSM. The consumption reached to a point where it became an issue to continue development. I tried out the newer back11 backend to see whether RAM usage and compilation time would reduce. Unfortunately in my state machine RAM usage about doubled.  
  
That's when I started looking into MSM with clang's `-ftime-trace` feature. It seems the culprit is the increased usage of `boost::fusion`, which does not make use of C++11's variadic templates. I analyzed the parts of the implementation that led to the high resource consumption, which were mainly the fold operations from mpl as well as the usage of fusion in general. I then applied the following optimizations:  
  
- Replaced lots of `boost::mpl` usages with `boost::mp11` equivalents  
- Replaced the `fusion`-based handling of the substate list with a `std::tuple`  
  
I've conducted some benchmarks to get an idea about how much optimization I had gained. The benchmarks were run on a ~Ryzen 7 4800HS with clang 18.1.3~ i7-6700K 4GHz with clang 19.1.4, C++20 and a RelWithDebInfo build, the code is based on [this benchmark](https://github.com/fgoujeon/fsm-benchmark):  
  
*Had to run the benchmarks again because I forgot to turn off `ftime-trace`. State of that code is WIP from a [branch with further optimizations](https://github.com/chandryan/msm/tree/feat/poc-mp11-more-optimizations)*  
  
| Name                        | Compile / sec | RAM / MB | Runtime / sec  |  
|-----------------------------|---------------|----------|----------------|  
| back                        | 17            | 955      | 7              |  
| back_favor_compile_time     | 22            | 1000     | 8              |  
| back11                      | 42            | 2916     | 7              |  
| backmp11                    | 8             | 504      | 3              |  
| backmp11_favor_compile_time | 10            | 465      | 4              |  
  
  
**Summary: The backend `msm:backmp11` is about 2 ~..3~ times more efficient than `msm::back`.**  
Which applies to one big non-hierarchical state machine; for hierarchical state machines I'd expect even more difference due to the additional recursion involved.  
  
I will create a draft PR with my PoC that shows the applied code changes (#83). It is compatible with all existing features and, as seen in the benchmark results, also supports the `favor_compile_time` option.   
  
I would like to contribute to MSM and polish the PoC to a production-ready state, if you would you like to have it in your project? :-)   
  
~FYI the PoC currently requires C++14 to compile, I still need to adapt it to comply with C+11.~ It now also builds with C++11.

---

## Comment 1

> Username: henry-ch  
> Created at: 2025-02-19 08:49:59 UTC  
> Url: https://github.com/boostorg/msm/issues/82#issuecomment-2667944837  

I love the idea. It was on my list but I never found time for it. Please give me so time to review it.  
Thank you for the cool work!

---

## Comment 2

> Username: chandryan  
> Created at: 2025-03-09 16:08:36 UTC  
> Url: https://github.com/boostorg/msm/issues/82#issuecomment-2708937010  

> I love the idea. It was on my list but I never found time for it. Please give me so time to review it. Thank you for the cool work!  
  
Thanks a lot :-)  
In the meantime I have continued my work beyond utilizing Mp11 and also took a deep dive into optimizing hierarchical state machines. The results look even more promising and the improvement factor in my benchmarks is around factor 4, for both compile time and RAM usage 🥳   
  
I will concentrate on improving the maturity of the code and get decent test coverage as next steps. Until then I'd recommend you to wait with a thorough review as not to waste your time reviewing the yet incomplete PoC code.

---

## Comment 3

> Username: chandryan  
> Created at: 2025-04-15 13:23:26 UTC  
> Updated at: 2025-04-15 14:09:19 UTC  
> Url: https://github.com/boostorg/msm/issues/82#issuecomment-2805046883  

I have an update to share regarding the state of the code and some new benchmarks.  
  
## Current state of development  
  
I have reached a decent level of maturity with the `backmp11` now:  
  
- Implemented further optimizations and a new dispatch mechanism for the `favor_compile_time` policy  
- All tests from the MSM repo are passing  
- Conducted more benchmarks  
  
I have pushed the changes with the new `backmp11` backend to [my fork](https://github.com/chandryan/msm-ng)'s develop branch. The most important points about the optimizations are summarized in a [README](https://github.com/chandryan/msm-ng/tree/develop/include/boost/msm/backmp11#readme).  
  
Eager to get your feedback and I hope it can be merged back to this repo eventually :-)  
Until then I will continue working in the fork if I find bugs or more things worthwhile optimizing.  
  
FYI:  
If you're interested in getting the new backend merged into this repo, I recommend to merge the [refactored tests](https://github.com/boostorg/msm/pull/89) first. I have extended the tests to cover the new backend based on this test code refactoring.  
  
## Latest benchmark results  
  
I have created a [separate repo](https://github.com/chandryan/fsm-benchmarks) for the benchmarks. I'm testing 2 scenarios with various MSM configurations as well as SML for comparison.  
  
Detailed results are shown in the repo's [README](https://github.com/chandryan/fsm-benchmarks/blob/main/README.md). It looks like the `backmp11` backend has the potential to get MSM to the lead position for C++ state machine libs, both for compilation resources as well as execution speed 😎   
  
Highlights for less complex, "flat" state machines:  
- `backmp11` *compiles up to 3x faster and uses up to 2.5x less memory* than the other backends  
- It also *compiles almost twice as fast as SML* and the runtime of favor_runtime_speed is on par with SML  
  
Highlights for more complex, "nested" hierarchical state machines:  
- `backmp11` *compiles up to 6x faster and uses up to 5x less memory* than the other backends  
- The `favor_compile_time` policy *runs almost 7x faster* than the previous compile-time-optimized version due to a new dispatch  mechanism that can dispatch to submachines in O(1) runtime instead of O(N) (N being no. of events in the SM)  
- It also *compiles almost 3x faster than SML and even needs about 30% less RAM*. The runtime of favor_runtime_speed is on par with SML  
  - with the `favor_compile_time` policy it compiles even 4x times faster and uses only half the RAM for compilation that SML requires
