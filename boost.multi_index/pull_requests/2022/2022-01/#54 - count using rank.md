# #54 count using rank [Merged]

> Username: DamianSawicki  
> Created at: 2022-01-26 16:05:52 UTC  
> Updated at: 2022-01-27 13:29:57 UTC  
> Merged at: 2022-01-27 08:34:43 UTC  
> Closed at: 2022-01-27 08:34:43 UTC  
> Url: https://github.com/boostorg/multi_index/pull/54  

**Purpose.** The purpose of this contribution is to replace in `ranked_index` the ordinary implementation of `count(x)` and `count(x,comp)` from `ordered_index` with an implementation using rank, which reduces the time complexity from respectively `log(n)+count(x)` and `log(n)+count(x,comp)` down to `log(n)`.  
  
**Explanation.** The implementation from `ordered_index` uses `std::distance`, which travels linearly between the beginning and end of the range. When `count(x)`/`count(x,comp)` is comparable with `n`, the computational cost is high. For `ranked_index`, one can subtract the corresponding values of rank instead.  
  
**Motivation.** As opposed to `contains`, the usage of methods called `count`  is best justified when the return values can be larger than `1`. Example use cases when `count(x)` is large include boolean indices, but also restricted numeric indices like the age of people (particularly for special social groups like university students) or even strings like names. Another use case is when a developer is interested in grouping values of an index according to some comparison predicate. For example, one can take the already restricted index of people's ages and group its values into age groups `0..9`, `10..19` etc. with a comparison predicate `comp` comparing `age/10`.  
  
**Changes in the repository.** The additions of this pull request consist of  
  
- the new implementation of `count(x)` and `count(x,comp)` in `include/boost/multi_index/ranked_index.hpp`;  
- regression tests comparing the new `count` functions in `ranked_index` with those in `ordered_index` (added to `test/Jamfile.v2` and `test/test_all_main.cpp`);  
- a benchmark in `example/count_benchmark.cpp` showing that the new implementation performs similarly to the implementation in `ordered_index` when `count(x,comp) <= 1` and highly outperforms it for large values of `count(x,comp)` (see the respective commit message for a summary of results of running benchmark on my machine);  
- an update of documentation with the new complexity of `count(x)` and `count(x,comp)` for `ranked_index`.  
  
**Tests.** The code successfully passes the existing tests and the newly added tests of the proposed changes, with the exception of compilation errors in job 20 (`posix (TOOLSET=clang COMPILER=clang++-5.0 CXXSTD=03, Job 20, boost, clang-5.0, ubuntu-20.04...`) identical to those (https://drone.cpp.al/boostorg/multi_index/89/21/2) occurring for the branch `master` of `boostorg/multi_index`

---

## Comment 1

> Username: joaquintides  
> Created_at: 2022-01-27 08:41:21 UTC  
> Url: https://github.com/boostorg/multi_index/pull/54#issuecomment-1022972863  

Hi Damian,  
  
Thank you for your contribution. Using rank operations to implement `count` in ranked indices is an unconditional improvement and I will add it to the library.  
  
Special thanks also for the thorough PR you've submitted --many people just propose the raw change to the source code and forget about documentation, testing etc., so this is fantastic. I have to say, though, that the PR maybe goes overboard and some pieces (like the dedicated test) are not really needed: what I've done then is merge your stuff into a branch of mine so that your contribution record is not lost and I can take this material and probably simplify it before merging into develop, which I plan to do in the following days so that we're on time for Boost 1.79.  
  
Best regards,

---

## Comment 2

> Username: DamianSawicki  
> Created_at: 2022-01-27 13:29:57 UTC  
> Url: https://github.com/boostorg/multi_index/pull/54#issuecomment-1023212419  

Hi Joaquín,  
  
Thank you very much for merging my change and for creating this great library in the first place. Sure, quite thorough tests and the time-consuming benchmark were intended mostly to accompany the PR, so it makes perfect sense to slim these down. Many thanks for taking care of it.  
  
Best regards,  
Damian

---
