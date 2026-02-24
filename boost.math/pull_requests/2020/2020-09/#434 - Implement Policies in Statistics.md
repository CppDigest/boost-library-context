# #434 Implement Policies in Statistics [Merged]

> Username: mborland  
> Created at: 2020-09-10 22:33:34 UTC  
> Updated at: 2021-01-22 16:40:39 UTC  
> Merged at: 2021-01-22 14:42:16 UTC  
> Closed at: 2021-01-22 14:42:17 UTC  
> Url: https://github.com/boostorg/math/pull/434  

This PR is working through implementing execution policies for uni-variate statistics. As with the STL there will be additional overloads for each function, and the default policy will be sequential.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-09-10 22:54:28 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-690773720  

This is definitely useful; did you see #248? I never finished for reasons I can't recall.  
  
Could you make sure that your parallel reductions agree with the algorithms presented here?  
  
Bennett, Janine, et al. "Numerically stable, single-pass, parallel statistics algorithms." Cluster Computing and Workshops, 2009. CLUSTER'09. IEEE International Conference on. IEEE, 2009.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-10 22:59:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-486366831  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
  40 |+         else
  41 |+         {
  42 |+             return std::reduce(exec, first, last, 0.0) / std::distance(first, last);
```

> Username: NAThompson  
> Created_at: 2020-09-10 22:59:29 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r486679105  

Take a read the Higham's "Accuracy and Stability of Numerical Algorithms", equation 1.6a,b to see why I did it this way and not with `std::reduce`.  
  
Another good reference is:  
  
Robert F Ling. Comparison of several algorithms for computing sample means and variances. Journal of the American Statistical Association, 69(348): 859–866, 1974

> Username: mborland  
> Created_at: 2020-09-11 02:16:51 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r486733568  

What about for integer arithmetic? From [26.7.1](http://www.open-std.org/Jtc1/sc22/wg21/docs/papers/2016/p0024r1) ```reduce applies binary_op in an unspecified order, which yields a non-deterministic result for non-associative or non-commutative binary_op such as floating-point addition.``` I will have to look at those references for the other cases.

> Username: NAThompson  
> Created_at: 2020-09-11 13:04:45 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r487030637  

Yeah, for integer arithmetic this won't work; then again the result needs to be cast to a floating point type anyway.  
  
If indeed we wished to support integer arithmetic then we'd need to figure out whether we want to cast every element to double and use the floating point algorithm or use the naive algorithm and then cast the sum to double. Overflow is the danger of the latter.


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-10 23:00:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-486367274  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
  47 |+         std::size_t elements {std::distance(first, last)};
  48 |+         std::valarray<Real> mu {0, 0, 0, 0};
  49 |+         std::valarray<Real> temp {0, 0, 0, 0};
```

> Username: NAThompson  
> Created_at: 2020-09-10 23:00:39 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r486679487  

Isn't `valarray` on the verge of deprecation?

> Username: mborland  
> Created_at: 2020-09-11 02:26:15 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r486736066  

Not that I am aware of. The implementation in the Intel MKL has some pretty serious optimization. It certainly has its idiosyncrasies but it is good at what it does.

> Username: NAThompson  
> Created_at: 2020-09-11 13:05:16 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r487030914  

Cool; but let's check the generated asm on godbolt and make sure it looks good.


---

## Comment 4

> Username: mborland  
> Created_at: 2020-09-11 02:03:53 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-690828783  

> This is definitely useful; did you see #248? I never finished for reasons I can't recall.  
>   
> Could you make sure that your parallel reductions agree with the algorithms presented here?  
>   
> Bennett, Janine, et al. "Numerically stable, single-pass, parallel statistics algorithms." Cluster Computing and Workshops, 2009. CLUSTER'09. IEEE International Conference on. IEEE, 2009.  
  
I did; I was scrolling through stale PRs, and Issues to see what would be worthwhile to work next. I can give it a read this weekend.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-09-11 03:03:45 UTC  
> Updated_at: 2020-09-11 13:05:34 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-690845080  

I believe I now recall what happened: I did some performance benchmarks and found it very hard to get the parallel performance to exceed the single-threaded; the arithmetic intensity is pretty low for many of these. Also, compiler support was tough on Ubuntu; had to install tbb via github!  
  
Also, IIRC the projections had very poor compiler support.  
  
But maybe things have stabilized a bit . . .

---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2020-09-11 13:07:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-486788839  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 137 |+             std::atomic<double> M {*first};
 138 |+             std::atomic<double> Q {0};
 139 |+             std::atomic<double> k {2};
```

> Username: NAThompson  
> Created_at: 2020-09-11 13:07:26 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r487032160  

I dunno man! Using atomics here is gonna be expensive!  
  
I'd get some performance benchmarks written before you get too far into this.  
  
Also, the Monte-Carlo integration class has an algorithm for pooling variance that you might enjoy looking at.

> Username: mborland  
> Created_at: 2020-09-11 16:00:06 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r487140648  

I just wanted to see how this would work with automatic partitioning from `std::for_each`. The answer is not super well.

> Username: NAThompson  
> Created_at: 2020-09-11 16:26:24 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r487155025  

Got it; probably should give you some time before sniping at everything you try!  
  
In any case, looks like I never committed a google benchmark for these functions; do you mind doing that? Should be easy now that you've got the first one done.

> Username: mborland  
> Created_at: 2020-09-11 19:53:08 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r487258849  

Can do. I need to validate that the usefulness anyway.


---

## Comment 7

> Username: mborland  
> Created_at: 2020-09-11 20:11:05 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-691290840  

@NAThompson have you seen the [latest draft of the stats proposal](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p1708r2.pdf)l? I think the stats class would be a useful addition. I am not sure we could implement it as constexpr without pushing the standard forward to c++20 to have constexpr iterators, but it still seems like it would be nice to input a data set then have everything calculated and stored.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2020-09-11 20:49:45 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-691305522  

@mborland : I like that they use the bitset to specify which statistics they want to use; it actually solves a design problem I had in this.  
  
Once you get the execution policies working it might be good to write the authors of that proposal with the results.

---

## Comment 9

> Username: mborland  
> Created_at: 2020-10-11 19:41:36 UTC  
> Updated_at: 2020-10-11 19:44:08 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-706757671  

Some initial benchmarks:  
```  
-------------------------------------------------------------------------------------------------------  
Benchmark                                                             Time             CPU   Iterations  
-------------------------------------------------------------------------------------------------------  
mean<int>/real_time_BigO                                           5.02 N          5.01 N      
mean<int>/real_time_RMS                                               0 %             0 %   
parallel_mean<int>/real_time_BigO                                  0.12 N          0.12 N      
parallel_mean<int>/real_time_RMS                                      1 %             1 %    
mean<double>/real_time_BigO                                        1.10 N          1.10 N      
mean<double>/real_time_RMS                                            0 %             0 %    
parallel_mean<double>/real_time_BigO                               0.24 N          0.24 N      
parallel_mean<double>/real_time_RMS                                   5 %             5 %   
  
variance<int>/real_time_BigO                                       5.16 N          5.15 N      
variance<int>/real_time_RMS                                           0 %             0 %    
parallel_variance<int>/real_time_BigO                              1.58 N          0.04 N      
parallel_variance<int>/real_time_RMS                                 26 %            86 %  
variance<double>/real_time_BigO                                    5.16 N          5.16 N      
variance<double>/real_time_RMS                                        0 %             0 %    
parallel_variance<double>/real_time_BigO                           1.62 N          0.04 N      
parallel_variance<double>/real_time_RMS                              25 %            85 %  
  
median<int>/real_time_BigO                                         1.45 N          1.45 N      
median<int>/real_time_RMS                                            14 %            15 %   
parallel_median<int>/real_time_BigO                                1.01 N          1.01 N      
parallel_median<int>/real_time_RMS                                    1 %             1 %  
median<double>/real_time_BigO                                      1.48 N          1.48 N      
median<double>/real_time_RMS                                          7 %             7 %     
parallel_median<double>/real_time_BigO                             1.22 N          1.22 N      
parallel_median<double>/real_time_RMS                                 3 %             3 %  
  
median_absolute_deviation<int>/real_time_BigO                      3.07 N          3.07 N      
median_absolute_deviation<int>/real_time_RMS                          9 %             9 %   
parallel_median_absolute_deviation<int>/real_time_BigO             2.32 N          2.32 N      
parallel_median_absolute_deviation<int>/real_time_RMS                 1 %             1 %   
median_absolute_deviation<double>/real_time_BigO                   2.61 N          2.60 N      
median_absolute_deviation<double>/real_time_RMS                       8 %             8 %    
parallel_median_absolute_deviation<double>/real_time_BigO          1.40 N          1.40 N      
parallel_median_absolute_deviation<double>/real_time_RMS              2 %             2 %   
  
gini_coefficient<int>/real_time_BigO                              10.12 N         10.11 N      
gini_coefficient<int>/real_time_RMS                                  14 %            14 %   
parallel_gini_coefficient<int>/real_time_BigO                     27.73 N         27.71 N      
parallel_gini_coefficient<int>/real_time_RMS                          7 %             7 %   
gini_coefficient<double>/real_time_BigO                           11.38 N         11.37 N      
gini_coefficient<double>/real_time_RMS                               18 %            18 %    
parallel_gini_coefficient<double>/real_time_BigO                  30.03 N         29.99 N      
parallel_gini_coefficient<double>/real_time_RMS                      12 %            12 %   
  
interquartile_range<double>/real_time_BigO                         2.41 N          2.41 N      
interquartile_range<double>/real_time_RMS                             8 %             8 %   
parallel_interquartile_range<double>/real_time_BigO                2.40 N          2.40 N      
parallel_interquartile_range<double>/real_time_RMS                    4 %             4 %   
```

---

## Comment 10

> Username: mborland  
> Created_at: 2020-11-03 22:43:49 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-721412727  

@NAThompson When you have time I believe this is in good shape to be reviewed. Almost all of the implementations use your original algorithms and recursively decompose the data set. The only error that I thought was unacceptably high was the calculation for M2 in `first_four_moments` for `long double` and `cpp_bin_float_50` which has some cascading effects. I am hoping a second set of eyes will catch a small mistake.

---

## Comment 11

> Username: mborland  
> Created_at: 2020-11-19 17:22:19 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-730520827  

Some updated benchmarks now that I have my workstation back (I was moving and using an old macbook):  
  
```  
-------------------------------------------------------------------------------------------------------  
Benchmark                                                             Time             CPU   Iterations  
-------------------------------------------------------------------------------------------------------  
mean<int>/real_time_BigO                                           1.03 N          1.03 N      
parallel_mean<int>/real_time_BigO                                  0.12 N          0.12 N      
mean<double>/real_time_BigO                                        1.04 N          1.04 N      
parallel_mean<double>/real_time_BigO                               0.24 N          0.24 N      
  
variance<int>/real_time_BigO                                       5.15 N          5.14 N      
parallel_variance<int>/real_time_BigO                              1.54 N          0.04 N      
variance<double>/real_time_BigO                                    5.19 N          5.18 N  
parallel_variance<double>/real_time_BigO                           1.55 N          0.05 N   
  
skewness<int>/real_time_BigO                                       5.21 N          5.19 N  
parallel_skewness<int>/real_time_BigO                              1.60 N          0.04 N  
skewness<double>/real_time_BigO                                    5.15 N          5.14 N  
parallel_skewness<double>/real_time_BigO                           1.60 N          0.04 N  
  
first_four_moments<int>/real_time_BigO                             5.39 N          5.38 N  
parallel_first_four_moments<int>/real_time_BigO                    1.57 N          0.04 N  
first_four_moments<double>/real_time_BigO                          5.41 N          5.40 N  
parallel_first_four_moments<double>/real_time_BigO                 1.60 N          0.04 N   
  
kurtosis<int>/real_time_BigO                                       5.39 N          5.39 N  
parallel_kurtosis<int>/real_time_BigO                              1.58 N          0.04 N  
kurtosis<double>/real_time_BigO                                    5.42 N          5.41 N  
parallel_kurtosis<double>/real_time_BigO                           1.59 N          0.04 N  
  
median<int>/real_time_BigO                                         1.23 N          1.23 N  
parallel_median<int>/real_time_BigO                                1.01 N          1.01 N  
median<double>/real_time_BigO                                      1.55 N          1.55 N  
parallel_median<double>/real_time_BigO                             1.31 N          1.31 N  
  
median_absolute_deviation<int>/real_time_BigO                      2.99 N          2.98 N  
parallel_median_absolute_deviation<int>/real_time_BigO             2.32 N          2.31 N  
median_absolute_deviation<double>/real_time_BigO                   2.44 N          2.43 N  
parallel_median_absolute_deviation<double>/real_time_BigO          1.40 N          1.40 N  
  
gini_coefficient<int>/real_time_BigO                              10.74 N         10.73 N  
parallel_gini_coefficient<int>/real_time_BigO                      0.52 N          0.52 N  
gini_coefficient<double>/real_time_BigO                           11.73 N         11.72 N  
parallel_gini_coefficient<double>/real_time_BigO                   0.81 N          0.81 N  
  
interquartile_range<double>/real_time_BigO                         2.29 N          2.29 N  
parallel_interquartile_range<double>/real_time_BigO                2.40 N          2.40 N  
```

---

## Comment 12

> Username: NAThompson  
> Created_at: 2020-11-19 17:31:56 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-730526293  

Wow this looks really good!  
  
I'm confused though; I feel like the CPU time should = threads*time, but it looks like it's the exact opposite?

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2020-11-19 18:15:25 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-730550180  

Google bench is IMO confusing for multithreaded tests, I just quickly tried this:  
  
```  
void thread_proc(unsigned n)  
{  
   auto f = [](unsigned n)  
   {  
      std::chrono::duration<unsigned, std::ratio<1, 10> > d(n);  
      std::this_thread::sleep_for(d);  
   };  
   std::thread t(f, n);  
   t.join();  
}  
  
void thread_test(benchmark::State& state)  
{  
   unsigned n = state.range(0);  
   for (auto _ : state)  
   {  
      thread_proc(n);  
   }  
   state.SetComplexityN(state.range(0));  
}  
  
  
BENCHMARK(thread_test)->RangeMultiplier(2)->Range(1 << 1, 1 << 20);  
```  
  
and got:  
  
```  
thread_test/2                                   202543985 ns        0.000 ns          100  
thread_test/4                                   402490410 ns        0.000 ns           10  
thread_test/8                                   802324240 ns        0.000 ns           10  
thread_test/16                                 1602629310 ns        0.000 ns           10  
thread_test/32                                 3201796500 ns        0.000 ns            1  
```  
  
So time looks to be reliable, but cpu not so much?

---

## Comment 14

> Username: mborland  
> Created_at: 2020-11-19 18:49:18 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-730567869  

I believe this is the issue:  
`  
By default, the CPU timer only measures the time spent by the main thread. If the benchmark itself uses threads internally, this measurement may not be what you are looking for. Instead, there is a way to measure the total CPU usage of the process, by all the threads.  
`  
  
If you run the thread_test with:   
`BENCHMARK(thread_test)->RangeMultiplier(2)->Range(1 << 1, 1 << 20)->MeasureProcessCPUTime()->UseRealTime();`  
  
the results are a more reasonable:  
```  
2020-11-19T21:45:54+03:00  
Running ./stats_benchmark  
Run on (12 X 4500 MHz CPU s)  
CPU Caches:  
  L1 Data 32 KiB (x6)  
  L1 Instruction 32 KiB (x6)  
  L2 Unified 256 KiB (x6)  
  L3 Unified 12288 KiB (x1)  
Load Average: 1.22, 1.33, 0.78  
-------------------------------------------------------------------------------------  
Benchmark                                           Time             CPU   Iterations  
-------------------------------------------------------------------------------------  
thread_test/2/process_time/real_time        200176299 ns        81472 ns            3  
thread_test/4/process_time/real_time        400235147 ns        83652 ns            2  
thread_test/8/process_time/real_time        800256134 ns       103176 ns            1  
thread_test/16/process_time/real_time      1600253804 ns        82747 ns            1  
thread_test/32/process_time/real_time      3200307348 ns       152016 ns            1  
thread_test/64/process_time/real_time      6400255888 ns       103569 ns            1  
```  
  
Here is the full snippet from the docs:  
```  
// Measure the time spent by the main thread, use it to decide for how long to  
// run the benchmark loop. Depending on the internal implementation detail may  
// measure to anywhere from near-zero (the overhead spent before/after work  
// handoff to worker thread[s]) to the whole single-thread time.  
BENCHMARK(BM_OpenMP)->Range(8, 8<<10);  
  
// Measure the user-visible time, the wall clock (literally, the time that  
// has passed on the clock on the wall), use it to decide for how long to  
// run the benchmark loop. This will always be meaningful, an will match the  
// time spent by the main thread in single-threaded case, in general decreasing  
// with the number of internal threads doing the work.  
BENCHMARK(BM_OpenMP)->Range(8, 8<<10)->UseRealTime();  
  
// Measure the total CPU consumption, use it to decide for how long to  
// run the benchmark loop. This will always measure to no less than the  
// time spent by the main thread in single-threaded case.  
BENCHMARK(BM_OpenMP)->Range(8, 8<<10)->MeasureProcessCPUTime();  
  
// A mixture of the last two. Measure the total CPU consumption, but use the  
// wall clock to decide for how long to run the benchmark loop.  
BENCHMARK(BM_OpenMP)->Range(8, 8<<10)->MeasureProcessCPUTime()->UseRealTime();  
```

---

## Comment 15

> Username: mborland  
> Created_at: 2020-11-21 18:55:49 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-731621156  

Here is the current performance of the last remaining function (mode):  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
mode<int>/real_time_BigO                      5.52 N          5.51 N  
parallel_mode<int>/real_time_BigO           337.80 N        337.31 N   
mode<double>/real_time_BigO                   9.08 N          9.07 N  
parallel_mode<double>/real_time_BigO        319.07 N        318.71 N  
```  
Obviously the current implementation of pre-processing all the data using an `unordered_map` did not work out like I had hoped. I am not sure how to decompose the problem like is done for most of the other functions. Any input/papers would be appreciated. I have not seen much on the topic. Maybe making this parallel is not particularly useful or in demand?

---

## Comment 16

> Username: mborland  
> Created_at: 2020-11-27 06:44:28 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-734673744  

AppVeyor results look like they should. Travis completed the most recent build 8 days ago, but has not started processing the next item in the queue. Is this a known issue? I think I saw something on the mailing list recently about Travis hanging in other libraries.

---

## Comment 17

> Username: NAThompson  
> Created_at: 2020-11-27 14:36:06 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-734864039  

> Is this a known issue?  
  
Not a known issue, but maybe related to the AWS downtime?

---

## Comment 18

> Username: mborland  
> Created_at: 2020-11-27 16:07:10 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-734899931  

[Status shows they did not have AWS issues](https://www.traviscistatus.com/#day). Does boost have a paid organizational account? If not the queue for open source linux build time being 10x what they process at a time would explain the delay.

---

## Comment 19

> Username: NAThompson  
> Created_at: 2020-11-27 17:24:00 UTC  
> Updated_at: 2020-11-27 17:29:54 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-734929464  

I'm pretty sure it's a free tier account. Personally I'd prefer to remove old compilers to speed up the Travis jobs before spending cash . . .

---

## Comment 20

> Username: mborland  
> Created_at: 2020-11-30 04:39:24 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-735539967  

Travis finally got around to doing its thing, and the results are clean. Pending any further review this is good to go from my end.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2020-11-30 14:39:43 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-735825505  

@mborland : Gimme a few days to get around to this; please reping me if I don't have it done by Friday!

---

## Comment 22

> Username: mborland  
> Created_at: 2020-12-05 08:18:05 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-739145373  

@NAThompson I forgot that I left a TODO in this, so if you have started reviewing I made a trivial change to one of the assertions.

---

## Review 23 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 15:37:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545595683  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 234 |-             static_assert(!std::is_same_v<Real, long double>, "Error for parallel calculation using long double exceeds 100 Epsilon");
 233 |+             static_assert(!std::is_same_v<Real, long double> ^ !std::is_trivially_constructible_v<Real>, 
 234 |+                           "Error for parallel calculation using long double or aribitrary precision types is excessivly large (>100 epsilon).");
```

> Username: NAThompson  
> Created_at: 2020-12-05 15:37:10 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536810154  

Do we have a good idea of why this is happening just for multiprecision types?  
  
For instance, is it happening for mpfr types as well?  
  
And have you normalized the error relative to the condition number?

> Username: mborland  
> Created_at: 2020-12-06 10:41:14 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537010621  

I tried it with `cpp_bin_float_50` and `float128`, and for both types the residual error for M2 is 5.55112e-17. As for the why? I am not quite sure. That error cascaded to others like kurtosis that use `first_four_moments` where the error remained in the realm of e-17.

> Username: jzmaddock  
> Created_at: 2020-12-06 11:00:47 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537013927  

When you see that, it's strongly suggestive of a stray double precision value in the calculation somewhere.  What's the precision for 80-bit long double?  One way to try and track this down is to set a breakpoint in cpp_bin_float's convert-from-double code (see assign_float in cpp_bin_float.hpp:295) and see if it get's triggered and with what values.

> Username: mborland  
> Created_at: 2020-12-06 14:53:42 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537054336  

@jzmaddock You are correct. `auto` was deducing the wrong type in the calculation of M2. Everything looks fine with explicit types.


---

## Comment 24

> Username: NAThompson  
> Created_at: 2020-12-05 15:46:52 UTC  
> Updated_at: 2020-12-05 16:00:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-739312646  

There's a syntax error somewhere in the `statistics/univariate_statistics.qbk` file:  
  
```  
math/doc$ ../../../b2  
statistics/univariate_statistics.qbk:397: error: Element not allowed in this context.  
Error: Error count: 1.  
```  
  
This diff fixes it:  
  
```  
[heading References]  
   
 * Higham, Nicholas J. ['Accuracy and stability of numerical algorithms.] Vol. 80. Siam, 2002.  
-* Philippe P. Pébay: ["Formulas for Robust, One-Pass Parallel Computation of Covariances and Arbitrary-Order Statistical Moments.] Technical Report SAND2008-6212, Sandia National Laboratories, September 2008.  
-* Chan, Tony F.; Golub, Gene H.; LeVeque, Randall J. (1979), "Updating Formulae and a Pairwise Algorithm for Computing Sample Variances.", Technical Report STAN-CS-79-773, Department of Computer Science, Stanford University.  
-* Pébaÿ, Philippe; Terriberry, Timothy; Kolla, Hemanth; Bennett, Janine (2016), "Numerically Stable, Scalable Formulas for Parallel and Online Computation of Higher-Order Multivariate Central Moments with Arbitrary Weights", Computational Statistics, Springer  
+* Philippe P. Pebay. ['Formulas for Robust, One-Pass Parallel Computation of Covariances and Arbitrary-Order Statistical Moments.] Technical Report SAND2008-6212, Sandia National Laboratories, September 2008.  
+* Tony F. Chan, Gene H. Golub, Randall J. LeVeque (1979), ['Updating Formulae and a Pairwise Algorithm for Computing Sample Variances.], Technical Report STAN-CS-79-773, Department of Computer Science, Stanford University.  
+* Philippe Pebay, Timothy Terriberry, Hemanth Kolla, Janine Bennett (2016), ['Numerically Stable, Scalable Formulas for Parallel and Online Computation of Higher-Order Multivariate Central Moments with Arbitrary Weights], Computational Statistics, Springer  
+  
 [endsect]  
```  
  
(I think it might only need a newline before [endsect] to compile.)

---

## Comment 25

> Username: NAThompson  
> Created_at: 2020-12-05 16:02:33 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-739314534  

Do we understand why this is happening?  
  
> The use of parallel execution policies (std::execution::par or par_unseq) generally doubles the error introduced in the computation, but is still minimal.

---

## Review 26 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 16:04:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545597593  

📁 doc/statistics/univariate_statistics.qbk

```diff
 226 | 
 227 | The implementation follows [@https://doi.org/10.1137/1.9780898718027 Higham 1.6a].
 228 | The data is not modified and must be forward iterable.
```

> Username: NAThompson  
> Created_at: 2020-12-05 16:04:25 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536814463  

I think it now has to be random access, not just forward iterable.

> Username: mborland  
> Created_at: 2020-12-06 09:17:38 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536995743  

Forward iterators are all that is required; parallel tests with `std::forward_list` still pass. The only changes I made to your battery of unit tests was to adjust the tolerances.

> Username: NAThompson  
> Created_at: 2020-12-06 19:10:45 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537103654  

@mborland : Doesn't the complexity of the algorithm increase for `std::forward_list`?

> Username: mborland  
> Created_at: 2020-12-07 17:42:13 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537699614  

Yes, it will.


---

## Review 27 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 16:04:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545597628  

📁 doc/statistics/univariate_statistics.qbk

```diff
 214 | 
 215 |+ /Nota bene/: The default execution policy for every function is std::execution::seq.
 216 |+ The use of parallel execution policies (std::execution::par or par_unseq) generally doubles the error introduced in the computation, but is still minimal.
```

> Username: NAThompson  
> Created_at: 2020-12-05 16:04:57 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536814535  

Might be good to say "Parallel routines must use random access containers, not just forward iterable."


---

## Review 28 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 16:13:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545598126  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
  36 |+         for(auto it {first}; it != end; it += 4)
  37 |+         {
  38 |+             const ReturnType inv {ReturnType(1) / i};
```

> Username: NAThompson  
> Created_at: 2020-12-05 16:13:01 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536815739  

In the random access case, you make the reciprocal approximation, but not in the general case below. That could be one reason the error rates are higher.

> Username: mborland  
> Created_at: 2020-12-07 19:16:07 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537762320  

Commit cf08af7 changes the implementation slightly so that the random access case now handles all cases. The general case has been removed.


---

## Review 29 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 16:15:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545598357  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
  52 |+         }
  53 |+ 
  54 |+         return (num1 * std::valarray<ReturnType>(mu[std::slice(0,3,1)]).sum() + num2 * mu[3]) / ReturnType(elements);
```

> Username: NAThompson  
> Created_at: 2020-12-05 16:15:48 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536816187  

This should reference the appropriate equation in the reference for parallel reduction of means.


---

## Review 30 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 16:18:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545598515  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
  96 |+ 
  97 |+ // Global thread counter required for recursive calls. Reset before each parallel method is called
  98 |+ static std::atomic<unsigned> thread_counter {1};
```

> Username: NAThompson  
> Created_at: 2020-12-05 16:18:04 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536816536  

I admit to not understanding why this needs a global static.

> Username: mborland  
> Created_at: 2020-12-06 09:38:08 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536999622  

It guarantees thread-safe initialization vs using a local static variable. It is also atomic so that each thread can safely increment it in order to avoid over-subscription since I am using `std::launch::async` policy.

> Username: NAThompson  
> Created_at: 2021-01-17 14:57:33 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559192887  

A global counter is going to cause problems. This counter can be shared by two entirely independent calculations running at the same time. Using Janine Bennet's algorithm will get you past this.

> Username: mborland  
> Created_at: 2021-01-17 16:14:05 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559202741  

The intent here was that it is shared by two independent calculations to avoid over-subscription. Looking back the case where a user specifies a parallel execution policy from two different threads is unlikely. I can make it locally static without any real issue. The other option would be to delete the thread counter entirely, and change the async launch policy to let the runtime deal with thread synchronization. Do you have a particular opinion either way?

> Username: NAThompson  
> Created_at: 2021-01-17 16:33:18 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559205236  

>  Looking back the case where a user specifies a parallel execution policy from two different threads is unlikely.  
  
I don't understand why this is particularly unlikely; nor is probability a useful framing for predicting how code will be used. But nonetheless, imagine a Rest API where the server is multithreaded and each thread uses this class to launch threads.  
  
> The other option would be to delete the thread counter entirely, and change the async launch policy to let the runtime deal with thread synchronization. Do you have a particular opinion either way?  
  
I admit I don't see how this is gonna work, though if you flesh it out in code I might be able to understand. Personally I think the pairwise recursive decomposition is going to cause problems-some of which you've already encountered-but I feel it's very fundamental to the nature of the algorithm, and [Bennet's](https://link.springer.com/article/10.1007/s00180-015-0637-z) algorithm will be simpler.

> Username: mborland  
> Created_at: 2021-01-17 19:38:07 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559228164  

I should have something up in the next day. Chan only described the calculation of M1 and M2. The current implementation of `first_four_moments` follows EQN 3.1 out of the linked paper (I had previously seen it from [Terriberry](https://web.archive.org/web/20140423031833/http://people.xiph.org/~tterribe/notes/homs.html)). Converting from recursive decomposition to linear should not be overly strenuous.


---

## Review 31 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 16:21:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545598709  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
 132 |+             return variance_sequential_impl<ReturnType>(std::next(first, range_a), last);
 133 |+         }
 134 |+     })};
```

> Username: NAThompson  
> Created_at: 2020-12-05 16:21:14 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536817012  

Are you sure this needs to be done via recursion? It looks like the only thing that buys you is the ability to use the formula to pool two variances to pool all the variances. But the Pebay paper shows how to pool n variances without issue.

> Username: mborland  
> Created_at: 2020-12-06 09:51:05 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537001991  

It is done via recursion because I found success using Chan's pairwise algorithms. I did not try Pebay's method.

> Username: NAThompson  
> Created_at: 2021-01-17 14:58:44 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559193132  

I have no problem with Chan's pairwise algorithm, but if it requires a global it just isn't going to work.


---

## Review 32 [Commented]

> Username: NAThompson  
> Created_at: 2020-12-05 16:23:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-545598810  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
 186 |+     const auto range_b {elements - range_a};
 187 |+ 
 188 |+     thread_counter.fetch_add(2);
```

> Username: NAThompson  
> Created_at: 2020-12-05 16:23:00 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536817303  

Looks like you're using the same global static thread counter for this as you do for the variance, which could lead to race conditions.

> Username: mborland  
> Created_at: 2020-12-06 07:43:19 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r536979837  

The thread counter and `fetch_add` are both atomic.

> Username: NAThompson  
> Created_at: 2020-12-06 19:08:51 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537103349  

Just because the counter is atomic doesn't mean race conditions are impossible. The counter can become incorrect. For example, try launching two threads running this algorithm and the variance at the same time. Each will increment the atomic counter, but then read a value that is incorrect.

> Username: mborland  
> Created_at: 2020-12-07 17:49:10 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r537704563  

In the event that you run this and variance simultaneously the number of threads will be synced globally to avoid over-subscription. Commit 045d72b offers better synchronization.


---

## Comment 33

> Username: NAThompson  
> Created_at: 2020-12-05 16:27:04 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-739317296  

I get unused typedef warning and a compile fail on the performance tests:  
  
```  
$ make reporting/performance/univariate_statistics_performance.x  
g++-10 -g -Wall -Wextra --std=gnu++17 -fno-finite-math-only -march=native -Wfatal-errors -MMD -O2 -I../../ -I/usr/local/include -o reporting/performance/univariate_statistics_performance.x reporting/performance/univariate_statistics_performance.cpp -L/usr/local/lib -pthread -lbenchmark -lbenchmark_main -lmpfr -lquadmath  
In file included from ../../boost/math/statistics/univariate_statistics.hpp:10,  
                 from reporting/performance/univariate_statistics_performance.cpp:8:  
../../boost/math/statistics/detail/single_pass.hpp: In function 'ReturnType boost::math::statistics::detail::parallel_variance_impl(ForwardIterator, ForwardIterator)':  
../../boost/math/statistics/detail/single_pass.hpp:105:11: warning: typedef 'using Real = typename std::iterator_traits<_II>::value_type' locally defined but not used [-Wunused-local-typedefs]  
  105 |     using Real = typename std::iterator_traits<ForwardIterator>::value_type;  
      |           ^~~~  
../../boost/math/statistics/detail/single_pass.hpp: In function 'ReturnType boost::math::statistics::detail::parallel_first_four_moments_impl(ForwardIterator, ForwardIterator)':  
../../boost/math/statistics/detail/single_pass.hpp:181:11: warning: typedef 'using Real = typename std::iterator_traits<_II>::value_type' locally defined but not used [-Wunused-local-typedefs]  
  181 |     using Real = typename std::iterator_traits<ForwardIterator>::value_type;  
      |           ^~~~  
reporting/performance/univariate_statistics_performance.cpp: In function 'std::vector<_RealType> generate_random_vector(std::size_t, std::size_t)':  
reporting/performance/univariate_statistics_performance.cpp:49:31: error: 'is_complex' is not a member of 'boost'  
   49 |     else if constexpr (boost::is_complex<T>::value)  
      |                               ^~~~~~~~~~  
compilation terminated due to -Wfatal-errors.  
make: *** [reporting/performance/univariate_statistics_performance.x] Error 1  
```

---

## Comment 34

> Username: NAThompson  
> Created_at: 2020-12-05 16:28:23 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-739317453  

Could you also compile both the unit tests and the performance tests with `-fsanitize=thread` and make sure they are clean?

---

## Comment 35

> Username: mborland  
> Created_at: 2020-12-06 07:38:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-739467815  

> Could you also compile both the unit tests and the performance tests with `-fsanitize=thread` and make sure they are clean?  
  
Unit tests and performance tests are both clean with asan and tsan.

---

## Comment 36

> Username: pabristow  
> Created_at: 2020-12-06 17:55:42 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-739538805  

I think that this is in line 258  range [0,1] and  [0, 1 - 1/ /n/].  
  
You need a \ before square brackets to get a real bracket  
  
https://www.boost.org/doc/libs/master/doc/html/quickbook/syntax/phrase.html  
  
Single char escape  
The backslash may be used to escape a single punctuation character. The punctuation immediately after the backslash is passed without any processing. This is useful when we need to escape QuickBook punctuations such as [ and ]. For example, how do you escape the triple quote? Simple: \'\'\'  
  
Been there, done that - often :-(  
  
Aside  \math\doc\html4_symbols.qbk has quickbook templates for lots of non-ascii chars, and may aid readability.  
  
[template eacute[]'''&#xE9;'''] [/ e acute ]  
  
or by using  
  
Unicode escape  
You can enter any 16-bit unicode character by using \u followed by its 4 digit hexadecimal code, or a 32-bit character by using \U followed by an 8 digit hexadecimal code. eg.  
  
\u03B1 + \u03B2

---

## Comment 37

> Username: mborland  
> Created_at: 2020-12-13 19:50:53 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-744059106  

Once this is in I have another PR in a similar vein almost ready to go. I realized that since each of the sequential implementations now relies on a specified return type I could reduce the required language standard. With some ugly template syntax all of the sequential methods can be made available with C++11.

---

## Comment 38

> Username: mborland  
> Created_at: 2020-12-20 10:37:47 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-748590191  

@NAThompson If you have already taken off for the holidays I can merge my c++11 bindings into this PR. It is nothing ground breaking, but will increase the diff stat by ~1500 LOC. If nothing else it'll give Travis some time since it seems to be quite slow right now.

---

## Review 39 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-05 21:54:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-562165061  

📁 .github/workflows/ci.yml

```diff
   1 |+ # Copyright 2020 Evan Miller
   2 |+ # Copyright 2020 Matt Borland
```

> Username: NAThompson  
> Created_at: 2021-01-05 21:54:36 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552222171  

Shouldn't this file be part of a separate PR?

> Username: mborland  
> Created_at: 2021-01-06 16:35:27 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552786047  

Yes; this is identical to PR #476. I had it in here to speed up CI cycles but will pull it out.


---

## Review 40 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-05 21:55:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-562165447  

📁 .gitignore

```diff
   7 | test/cuda
```

> Username: NAThompson  
> Created_at: 2021-01-05 21:55:22 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552222458  

I believe this diff is already merged. I would squash merge this PR onto the HEAD another so as to not generate conflicts.

> Username: mborland  
> Created_at: 2021-01-06 16:37:04 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552787745  

It has been. Git shows no conflicts with base branch but I'll merge develop to be safe.


---

## Review 41 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-05 21:56:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-562165810  

📁 doc/math.qbk

```diff
   9 |     ]
  10 |-     [authors [Agrawal, Nikhar], [Bikineev, Anton], [Bristow, Paul A.], [Holin, Hubert], [Guazzone, Marco], [Kormanyos, Christopher], [Lalande, Bruno], [Maddock, John], [Miller, Evan], [Murphy, Jeremy W.], [Pulver, Matthew], [Råde, Johan], [Sobotta, Benjamin], [Sewani, Gautam], [Thompson, Nicholas], [van den Berg, Thijs], [Walker, Daryle], [Zhang, Xiaogang]]
  10 |+     [authors [Agrawal, Nikhar], [Bikineev, Anton], [Borland, Matthew], [Bristow, Paul A.], [Holin, Hubert], [Guazzone, Marco], [Kormanyos, Christopher], [Lalande, Bruno], [Maddock, John], [Miller, Evan], [Murphy, Jeremy W.], [Pulver, Matthew], [Råde, Johan], [Sobotta, Benjamin], [Sewani, Gautam], [Thompson, Nicholas], [van den Berg, Thijs], [Walker, Daryle], [Zhang, Xiaogang]]
```

> Username: NAThompson  
> Created_at: 2021-01-05 21:56:00 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552222769  

I think you should add this in a separate PR; you've contributed enough even without this PR.


---

## Comment 42

> Username: mborland  
> Created_at: 2021-01-06 17:05:53 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-755431245  

@NAThompson Your requested clean-up is complete. The only remaining CI failure was `C1128: number of sections exceeded object file format limit: compile with /bigobj`. I commented out all of the `std::execution::par_unseq` tests to drop the size by 1/3. The only implementations that vary based on `par` vs `par_unseq` are STL algorithms.

---

## Review 43 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-06 17:30:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-562903160  

📁 doc/statistics/univariate_statistics.qbk

```diff
 205 | 
 206 |- /Nota bene/: Some similar functionality is provided in [@https://www.boost.org/doc/libs/1_68_0/doc/html/accumulators/user_s_guide.html Boost Accumulators Framework].
 206 |+ /Nota bene/: Some similar functionality is provided in [@https://www.boost.org/doc/libs/1_75_0/doc/html/accumulators/user_s_guide.html Boost Accumulators Framework].
```

> Username: NAThompson  
> Created_at: 2021-01-06 17:30:26 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552837911  

@jzmaddock : Is there a way to always link to the latest boost version?

> Username: jzmaddock  
> Created_at: 2021-01-06 18:08:52 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552873267  

@NAThompson : use https://www.boost.org/doc/libs/release/doc/html/accumulators.html

> Username: NAThompson  
> Created_at: 2021-01-06 18:10:24 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552874631  

@mborland : Could you make that change?

> Username: mborland  
> Created_at: 2021-01-06 18:12:15 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552876169  

@NAThompson Done.


---

## Review 44 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-06 17:31:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-562903613  

📁 test/univariate_statistics_test.cpp

```diff
1115 |     test_mode<float>(std::execution::par);
1114 |-     test_mode<float>(std::execution::par_unseq);
1116 |+     //test_mode<float>(std::execution::par_unseq);
```

> Username: NAThompson  
> Created_at: 2021-01-06 17:31:02 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552838441  

Why are the `par_unseq` commented out?

> Username: mborland  
> Created_at: 2021-01-06 17:52:32 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552858389  

The only remaining CI failure was `C1128: number of sections exceeded object file format limit: compile with /bigobj`. I commented out all of the std::execution::par_unseq tests to drop the size by 1/3. The only implementations that vary based on par vs par_unseq are STL algorithms.

> Username: NAThompson  
> Created_at: 2021-01-06 17:58:35 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552863476  

Ok, I'm cool with that. Want to just delete this rather than comment it out?

> Username: mborland  
> Created_at: 2021-01-06 18:05:26 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r552869976  

I have deleted them.


---

## Comment 45

> Username: mborland  
> Created_at: 2021-01-11 16:23:24 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-758064874  

@NAThompson If you have no other changes this should be good to go; CI is finally clean.

---

## Comment 46

> Username: mborland  
> Created_at: 2021-01-13 16:43:49 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-759574212  

@NAThompson Ping. Once this is merged in I have the stats class outlined in the [WG21 Statistics Library Proposal ](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p1708r2.pdf) ready for review.

---

## Comment 47

> Username: mborland  
> Created_at: 2021-01-18 09:07:50 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-762101740  

@NAThompson The requested `first_four_moments_parallel_impl` using linear decomposition instead of recursion is in.

---

## Review 48 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-18 14:49:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-570557038  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
 180 |+     const auto elements = std::distance(first, last);
 181 |+     static unsigned num_threads;
 182 |+     if(elements/10 >= std::thread::hardware_concurrency())
```

> Username: NAThompson  
> Created_at: 2021-01-18 14:49:11 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559617866  

I think 10 is probably too low. Launching a thread should take at least [10 microseconds](https://lemire.me/blog/2020/01/30/cost-of-a-thread-in-c-under-linux/).  
  
Your benchmark shows that a sequential variance takes 5.16N nanoseconds, so just to ensure that you can't complete the computation before you launch the thread, you need N >= 10^4/5.16 ~ 2000.

> Username: mborland  
> Created_at: 2021-01-18 18:14:16 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559734014  

Makes sense to me; I figured 10 was going to be too low. `std::async` should attempt to use a thread pool with the launch policy here, but ultimately I believe it's implementation defined.


---

## Review 49 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-18 14:49:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-570557283  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
 230 |+         const Real delta = M1_b - M1_a;
 231 |+         
 232 |+         M1_a = (range_a * M1_a + range_b * M1_b) / n_ab;
```

> Username: NAThompson  
> Created_at: 2021-01-18 14:49:26 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559618062  

Could you put a reference to these equations?

> Username: mborland  
> Created_at: 2021-01-18 18:07:05 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559731338  

Added at the top of the function.


---

## Review 50 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-18 18:50:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-570722136  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
 119 |+     // https://lemire.me/blog/2020/01/30/cost-of-a-thread-in-c-under-linux/
 120 |+     if(elements/2000 >= std::thread::hardware_concurrency())
 121 |     {
```

> Username: NAThompson  
> Created_at: 2021-01-18 18:50:14 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559748100  

What happens if `std::thread::hardware_concurrency()` returns 0?  
  
Also, might be better to do `elements >= 2000*std::thread::hardware_concurrency()` than do the division.

> Username: NAThompson  
> Created_at: 2021-01-18 18:52:40 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559748948  

Am I correct in thinking that this model assumes that each thread generation costs 10us, sequentially, for 10j us of cost? I believe it costs the same to launch 10 threads as it does to launch 1.  
  
(I need to break out a pencil and figure out the threshold for when this should be faster . . .)

> Username: NAThompson  
> Created_at: 2021-01-18 19:05:04 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559753394  

Ok, assuming it take 10µs to launch j threads, independent of j, and that the memory bandwidth is not a constraint (a big assumption, but still better than nothing) the parallel calculation is faster whenever  
  
10 + 5.13e-3 N/j <= 5.13e-3N => N >= 10^4j/5.13(j-1).  
  
The j = 0 and j=1 case should shunt through the sequential case, obviously.

> Username: mborland  
> Created_at: 2021-01-20 18:30:37 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r561184351  

```  
diff --git a/include/boost/math/statistics/detail/single_pass.hpp b/include/boost/math/statistics/detail/single_pass.hpp  
index 00b2d9e67..8559d0b31 100644  
--- a/include/boost/math/statistics/detail/single_pass.hpp  
+++ b/include/boost/math/statistics/detail/single_pass.hpp  
@@ -114,20 +114,32 @@ ReturnType first_four_moments_parallel_impl(ForwardIterator first, ForwardIterat  
     using Real = typename std::tuple_element<0, ReturnType>::type;  
   
     const auto elements = std::distance(first, last);  
-    static unsigned num_threads;  
+    const unsigned max_concurrency = std::thread::hardware_concurrency() == 0 ? 2u : std::thread::hardware_concurrency();  
+    unsigned num_threads = 2u;  
       
+    // Threading is faster for: 10 + 5.13e-3 N/j <= 5.13e-3N => N >= 10^4j/5.13(j-1).  
+    const auto parallel_lower_bound = 10e4*max_concurrency/(5.13*(max_concurrency-1));  
+    const auto parallel_upper_bound = 10e4*2/5.13; // j = 2  
+  
     // https://lemire.me/blog/2020/01/30/cost-of-a-thread-in-c-under-linux/  
-    if(elements/2000 >= std::thread::hardware_concurrency())  
+    if(elements < parallel_lower_bound)  
     {  
-        num_threads = std::thread::hardware_concurrency();  
+        return detail::first_four_moments_sequential_impl<ReturnType>(first, last);  
     }  
-    else if(elements < 4000)  
+    else if(elements >= parallel_upper_bound)  
     {  
-        return detail::first_four_moments_sequential_impl<ReturnType>(first, last);  
+        num_threads = max_concurrency;  
     }  
     else  
     {  
-        num_threads = elements/2000;  
+        for(unsigned i = 3; i < max_concurrency; ++i)  
+        {  
+            if(parallel_lower_bound < 10e4*i/(5.13*(i-1)))  
+            {  
+                num_threads = i;  
+                break;  
+            }  
+        }  
```  
Here is a quick diff. It should phase in the number of threads up to the maximum.


---

## Review 51 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-18 18:51:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/434#pullrequestreview-570722499  

📁 include/boost/math/statistics/detail/single_pass.hpp

```diff
 115 | 
 116 |     const auto elements = std::distance(first, last);
 117 |     static unsigned num_threads;
```

> Username: NAThompson  
> Created_at: 2021-01-18 18:51:02 UTC  
> Updated_at: 2021-01-21 16:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/434#discussion_r559748407  

I'd remove the `static` here unless you have a compelling reason to have it.


---

## Comment 52

> Username: NAThompson  
> Created_at: 2021-01-20 13:13:00 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-763597389  

Other than the two small issues identified above, I'm happy with this.

---

## Comment 53

> Username: mborland  
> Created_at: 2021-01-20 17:24:54 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-763807413  

@NAThompson I should have the fixes in soon; working through how to fix all these CI failures. By and large they seem to be related to [TBB](https://github.com/oneapi-src/oneTBB).

---

## Comment 54

> Username: NAThompson  
> Created_at: 2021-01-20 17:54:11 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-763824323  

@mborland : You're not the first one to struggle with TBB!

---

## Comment 55

> Username: mborland  
> Created_at: 2021-01-22 14:33:03 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-765442056  

@NAThompson CI is clean. The only failure is: `multiprc_concept_check_1.cpp(42): fatal error C1060: compiler is out of heap space`. I haven't seen this before, and this PR does not affect the test set it came from. More recent CI cycles are showing green still so I do not think it is overly concerning.

---

## Comment 56

> Username: NAThompson  
> Created_at: 2021-01-22 14:42:41 UTC  
> Url: https://github.com/boostorg/math/pull/434#issuecomment-765449498  

Looks good to me!

---
