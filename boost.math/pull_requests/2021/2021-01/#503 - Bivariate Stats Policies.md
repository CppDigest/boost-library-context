# #503 Bivariate Stats Policies [Merged]

> Username: mborland  
> Created at: 2021-01-28 16:43:11 UTC  
> Updated at: 2021-01-30 16:18:06 UTC  
> Merged at: 2021-01-30 16:09:13 UTC  
> Closed at: 2021-01-30 16:09:13 UTC  
> Url: https://github.com/boostorg/math/pull/503  

Adds execution policies to bivariate statistics. Benchmarks:  
  
```  
------------------------------------------------------------------------------------  
Benchmark                                          Time             CPU   Iterations  
------------------------------------------------------------------------------------  
seq_covariance<double>/real_time_BigO           5.22 N          5.22 N      
seq_covariance<double>/real_time_RMS               1 %             1 %   
par_covariance<double>/real_time_BigO           0.75 N          0.28 N      
par_covariance<double>/real_time_RMS              45 %            94 %  
   
seq_correlation<double>/real_time_BigO          3.22 N          3.22 N      
seq_correlation<double>/real_time_RMS              3 %             3 %  
par_correlation<double>/real_time_BigO          0.65 N          0.27 N      
par_correlation<double>/real_time_RMS             42 %            91 %  
```

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-28 18:23:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/503#pullrequestreview-578619695  

📁 include/boost/math/statistics/bivariate_statistics.hpp

```diff
  94 |+     else
  95 |+     {
  96 |+         for(unsigned i = 3; i < max_concurrency; ++i)
```

> Username: NAThompson  
> Created_at: 2021-01-28 18:23:52 UTC  
> Url: https://github.com/boostorg/math/pull/503#discussion_r566312973  

Can't you just calculate the `num_threads` directly instead of looping?

> Username: mborland  
> Created_at: 2021-01-29 17:51:49 UTC  
> Url: https://github.com/boostorg/math/pull/503#discussion_r566992497  

I can't think of a way besides the substitution used here to calculate the minimum number of threads.


---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-01-29 14:23:26 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-769834446  

@mborland : Really like these diffs.  
  
Quick question: Could you run the parallel benchmarks under `perf record`, and create a [flamegraph](https://github.com/brendangregg/FlameGraph) and a timechart using [hotspot](https://github.com/KDAB/hotspot)? I'm interested in how effectively the threads are being utilitized?

---

## Comment 3

> Username: mborland  
> Created_at: 2021-01-29 17:10:13 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-769931614  

@NAThompson I am not sure if this is totally what you are looking for.   
![Flamegraph](https://user-images.githubusercontent.com/3745830/106305220-08bdd200-6222-11eb-9aec-74c06e9d9a61.png)  
![Timegraph](https://user-images.githubusercontent.com/3745830/106305238-0c515900-6222-11eb-9047-4333da32b3a0.png)

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-01-29 18:14:41 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-769965748  

That does tell me what I need to know, namely that there aren't any particularly weird threading things consuming a bunch of time.  
  
Could you screenshot the timeline at the bottom?

---

## Comment 5

> Username: mborland  
> Created_at: 2021-01-29 18:54:15 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-769985499  

Here is the longest sample. There are over 150k processes in the list.  
![Timeline_2](https://user-images.githubusercontent.com/3745830/106315078-53464b00-6230-11eb-9436-919afeb13e03.png)

---

## Comment 6

> Username: NAThompson  
> Created_at: 2021-01-29 19:35:00 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-770006103  

@mborland : Wut! Why?

---

## Comment 7

> Username: mborland  
> Created_at: 2021-01-30 07:43:01 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-770173040  

I tried again and ran this reduced benchmark only:  
```  
BENCHMARK_TEMPLATE(par_covariance, double)->RangeMultiplier(2)->Range(1 << 20, 1 << 20)->Complexity(benchmark::oN)->UseRealTime();  
```  
The timeline shows 6072 processes, and a counter shows the covariance was calculated 506 times. 6072/506 = 12 which is the number of threads my machine has. Only the main thread has a complete timeline.  
  
![Reduced_Timeline](https://user-images.githubusercontent.com/3745830/106350580-e2d51380-629b-11eb-8e5e-d2a2fdcc1988.png)

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-01-30 14:30:12 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-770220713  

@mborland : That makes sense, and the flamegraph does look good.  
  
Does the Schubert paper have a better error estimate than Bennet's paper?

---

## Comment 9

> Username: mborland  
> Created_at: 2021-01-30 14:55:33 UTC  
> Url: https://github.com/boostorg/math/pull/503#issuecomment-770224029  

Schubert is much more pragmatic than Bennet. While he does not offer an equation for error bounds like she does he offers a lot more data comparing a large number of implementations.

---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-30 15:38:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/503#pullrequestreview-579765966  

📁 test/bivariate_statistics_test.cpp

```diff
  41 |- template<class Real>
  41 |+ #if (__cplusplus > 201700 || _MSVC_LANG > 201700) && (__GNUC__ > 9 || (__clang_major__ > 9 && defined __GLIBCXX__)  || _MSC_VER > 1927)
  42 |+ #include <execution>
```

> Username: NAThompson  
> Created_at: 2021-01-30 15:38:48 UTC  
> Updated_at: 2021-01-30 15:38:49 UTC  
> Url: https://github.com/boostorg/math/pull/503#discussion_r567262228  

Shouldn't this only need to be in the library code, not the user code?

> Username: mborland  
> Created_at: 2021-01-30 16:03:34 UTC  
> Url: https://github.com/boostorg/math/pull/503#discussion_r567264770  

This is here so that I could keep all the tests in one file. It is one giant if-else macro with a main for C++11&14 and a main for C++17&20. Normal users would not need this.


---
