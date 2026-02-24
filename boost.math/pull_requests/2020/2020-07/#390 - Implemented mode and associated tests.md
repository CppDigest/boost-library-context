# #390 Implemented mode and associated tests [Merged]

> Username: mborland  
> Created at: 2020-07-04 01:02:30 UTC  
> Updated at: 2021-01-26 19:25:32 UTC  
> Merged at: 2020-07-09 17:28:30 UTC  
> Closed at: 2020-07-09 17:28:30 UTC  
> Url: https://github.com/boostorg/math/pull/390  

A generic implementation of mode that allows for multi-modal distributions. Please let me know if you have any questions.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 02:12:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442581831  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 517 |+ auto mode(RandomAccessIterator first, RandomAccessIterator last)
 518 |+ {
 519 |+     using Real = typename std::iterator_traits<RandomAccessIterator>::value_type;
```

> Username: NAThompson  
> Created_at: 2020-07-04 02:12:31 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449729936  

Is `Real` the right language here? The probability that a set of real numbers has a mode is zero. I would use `Z`.

> Username: NAThompson  
> Created_at: 2020-07-04 02:19:54 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449730317  

I might even `static_assert` that `Z` is an integer type.

> Username: mborland  
> Created_at: 2020-07-04 02:35:57 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449731251  

I will make the change to Z. I went with Real for consistency with the rest of the file.


---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 02:14:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442581879  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 524 |+ 
 525 |+     std::vector<Real> modes {};
 526 |+     Size max_counter {};
```

> Username: NAThompson  
> Created_at: 2020-07-04 02:14:04 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449729992  

Are you sure `max_counter` will be initialized to zero? There might be a custom type that doesn't do that.


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 02:16:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442581959  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 540 |+         }
 541 |+         
 542 |+         Size current_count {std::count(sorted.begin(), sorted.end(), i)};
```

> Username: NAThompson  
> Created_at: 2020-07-04 02:16:03 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449730147  

The complexity of `std::count` is linear, so isn't this loop quadratic?

> Username: mborland  
> Created_at: 2020-07-04 02:42:25 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449731609  

It would be. I swapped the range based for loop out with an iterator so it can replace begin() in the count. That will reduce the range it is iterating over.


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 02:17:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442582023  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 543 |+         if(current_count > max_counter)
 544 |+         {
 545 |+             modes.clear();
```

> Username: NAThompson  
> Created_at: 2020-07-04 02:17:29 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449730207  

Isn't this call to clear pretty expensive?

> Username: mborland  
> Created_at: 2020-07-04 02:48:11 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449731906  

At worst it is linear, and should never be very large. Libstdc++ has it optimized to constant complexity for POD.

> Username: jzmaddock  
> Created_at: 2020-07-04 08:59:31 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449754684  

+1.  Clear should be as simple as setting the size of the vector to zero for POD types.  For non POD types the destructors would all be called.  I guess a more streamlined version in that case would be to resize the vector to 1, and then assign the new value?


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 02:18:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442582039  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 556 |+     }
 557 |+ 
 558 |+     BOOST_ASSERT_MSG(max_counter > 1, "Mode is undefined");
```

> Username: NAThompson  
> Created_at: 2020-07-04 02:17:59 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449730241  

I would probably prefer to return an empty vector rather than die with an assert.

> Username: jzmaddock  
> Created_at: 2020-07-04 08:51:24 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449754115  

+1, asserts are for testing the logic of our code, not for user-errors or anything which is expected.  So either an exception or an empty vector as Nick suggests.


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 02:19:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442582089  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 562 |+ 
 563 |+ template<class RandomAccessContainer>
 564 |+ inline auto mode(RandomAccessContainer & v)
```

> Username: NAThompson  
> Created_at: 2020-07-04 02:19:23 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449730290  

The other implementations return `auto` because they might return `double` for integer inputs, or `Real` for `Real` inputs, `Real` for complex inputs so on. But it appears this one always returns `std::vector<Real>`, so it might be nice to write that explicitly.


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 02:21:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442582152  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
  12 | #include <cmath>
  13 | #include <boost/assert.hpp>
  14 |+ #include <boost/math/tools/precision.hpp>
```

> Username: NAThompson  
> Created_at: 2020-07-04 02:21:03 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449730392  

I'd probably just use `std::numeric_limits<Z>::max()` so that we don't have to bring in this header.


---

## Comment 8

> Username: NAThompson  
> Created_at: 2020-07-04 02:22:53 UTC  
> Updated_at: 2020-07-04 02:31:54 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-653709678  

@mborland : Could you put a google benchmark file into `reporting/performance`? You could just `cp rsqrt_performance.cpp` for a template. Make sure to test the asymptotic complexity.

---

## Comment 9

> Username: mborland  
> Created_at: 2020-07-04 03:54:09 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-653717022  

@NAThompson The google benchmark file has been added as requested. I do not see asymptotic complexity in the results.

---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 14:56:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442614276  

📁 reporting/performance/test_mode.cpp

```diff
  31 |+ }
  32 |+ 
  33 |+ BENCHMARK_TEMPLATE(test_mode, float);
```

> Username: NAThompson  
> Created_at: 2020-07-04 14:56:02 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449779745  

I feel fairly confident that this should not compile with any floating point type. Otherwise we need to have a semantics for when two floating point numbers are approximately equal.  
  
As it stands, the mode is not robust against perturbations.

> Username: mborland  
> Created_at: 2020-07-04 15:43:13 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449783034  

I added the assertion for integral values with the message that floating point values have not been implemented. I see what you are getting at with the floating point comparison problem.


---

## Review 11 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 14:59:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442614449  

📁 reporting/performance/test_mode.cpp

```diff
  34 |+ BENCHMARK_TEMPLATE(test_mode, double);
  35 |+ BENCHMARK_TEMPLATE(test_mode, long double);
  36 |+ BENCHMARK_TEMPLATE(test_mode, int);
```

> Username: NAThompson  
> Created_at: 2020-07-04 14:59:55 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449780008  

I would try int32_t, int64_t, uint64_t, uint32_t, and maybe __int128_t.  
  
Then we need to measure the complexity. My apologies for recommending `rsqrt_performance.cpp` as a template, [chebyshev_clenshaw.cpp](https://github.com/boostorg/math/blob/develop/reporting/performance/chebyshev_clenshaw.cpp) is a better example for this.  
  
Could you also add a benchmark for the vector {0,1,2,3,4,5,6,...}  and {1,1,2,2,3,3,4,4, ...}? I want to see if there's some way to break the complexity of the algorithm.  
  
Also: {1,2,2,3,3,3,4,4,4,4,5,5,5,5,5, ....}

> Username: mborland  
> Created_at: 2020-07-04 16:29:00 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449786380  

Added those additional tests. Attached is a synopsis of the results.  
  
[Results.txt](https://github.com/boostorg/math/files/4873188/Results.txt)


---

## Review 12 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 15:04:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442614702  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 519 |+     using Size = typename std::iterator_traits<RandomAccessIterator>::difference_type;
 520 |+ 
 521 |+     std::vector<Z> sorted(first, last);
```

> Username: NAThompson  
> Created_at: 2020-07-04 15:04:51 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449780332  

There is an idiom in the standard library that sorting operations do not copy their input, they mutate it. See `std::nth_element`.  
  
There might be a case to add a `sorted_mode` call which takes sorted data by `const &`, and this can forward to that call. I've found in statistical workflows, there are lots of things that require sorted data, so it's not a bad assumption that sorting is part of a larger workflow.


---

## Review 13 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 17:00:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442620442  

📁 reporting/performance/test_mode.cpp

```diff
  36 |- BENCHMARK_TEMPLATE(test_mode, int);
  37 |- BENCHMARK_TEMPLATE(test_mode, cpp_bin_float_50);
 113 |+ BENCHMARK_TEMPLATE(test_mode, int)->RangeMultiplier(2)->Range(1<<1, 1<<22)->Complexity(benchmark::oN);
```

> Username: NAThompson  
> Created_at: 2020-07-04 16:56:10 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449788392  

Don't put the `benchmark::oN` key in these. First, because the expected complexity is `nlog(n)`, and second, because we're looking for performance bugs. If you specify the complexity class, then you'll never see that the best fit is (say) quadratic.  
  
Just do:  
  
```  
BENCHMARK_TEMPLATE(test_mode, int)->RangeMultiplier(2)->Range(1<<1, 1<<22)->Complexity();  
```

> Username: mborland  
> Created_at: 2020-07-04 17:20:53 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449790177  

Go it. The results are more sensical now.  
  
Worst values  
test_mode: 13.06 nlog(n)  
sequential: 2.48 nlog(n)  
sequential_pairs: 1.87 nlog(n)  
sequential_multiple: 1.55 nlog(n)


📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 522 |-     std::sort(sorted.begin(), sorted.end());
 523 |+     
 524 |+     if(!std::is_sorted(sorted.begin(), sorted.end()))
```

> Username: NAThompson  
> Created_at: 2020-07-04 16:58:54 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449788547  

I'm not convinced this is buying us much. `std::is_sorted` takes O(N) time if the vector is sorted, and `std::sort` also takes O(N) time if the vector is sorted.  
  
I think having a `sorted_mode` would also help us with the benchmarks, because your results are currently dominated by the time for sorting.

> Username: mborland  
> Created_at: 2020-07-04 17:41:06 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449791669  

With a separate sorted mode function the results validate the addition.  
  
Worst values  
test_mode: Unchanged  
sequential: 2.48 -> 2.01 nlog(n)  
sequential_pairs: 1.87 -> 1.40 nlog(n)  
sequential_multiples: 1.55 -> 1.08 nlog(n)

> Username: NAThompson  
> Created_at: 2020-07-04 18:43:47 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449796112  

Wait, shouldn't this be O(N) for the sorted input?


---

## Review 14 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 18:42:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442625417  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 586 |+     using Z = typename std::iterator_traits<RandomAccessIterator>::value_type;
 587 |+     std::vector<Z> sorted(first, last);
 588 |+     std::sort(sorted.begin(), sorted.end());
```

> Username: NAThompson  
> Created_at: 2020-07-04 18:42:58 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449796082  

I still recommend against copying, and instead recommend documenting that `mode` mutates its inputs.


---

## Comment 15

> Username: NAThompson  
> Created_at: 2020-07-04 19:07:21 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-653800784  

@mborland : Just ran the benchmark:  
  
```  
$ g++-9 -g --std=gnu++17 -ffast-math -fno-finite-math-only -march=native -Wfatal-errors -MMD -fvisibility=hidden -O3 -I../../ -I/usr/local/include -o reporting/performance/test_mode.x reporting/performance/test_mode.cpp -pthread -lbenchmark -lbenchmark_main -L/usr/local/lib  
$ ./reporting/performance/test_mode.x  
2020-07-04 15:05:05  
Running ./reporting/performance/test_mode.x  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 262K (x2)  
  L3 Unified 3145K (x1)  
Load Average: 2.90, 3.60, 3.28  
-------------------------------------------------------------------------------------------  
Benchmark                                                 Time             CPU   Iterations  
-------------------------------------------------------------------------------------------  
test_mode<int>/2                                        576 ns          574 ns      1256890  
test_mode<int>/4                                        670 ns          662 ns      1067155  
test_mode<int>/8                                        691 ns          687 ns      1000000  
test_mode<int>/16                                       754 ns          752 ns      1084565  
test_mode<int>/32                                       779 ns          772 ns      1155592  
test_mode<int>/64                                       808 ns          804 ns       858548  
test_mode<int>/128                                     1251 ns         1247 ns       564903  
test_mode<int>/256                                     2294 ns         2283 ns       324230  
test_mode<int>/512                                     4274 ns         4259 ns       175606  
test_mode<int>/1024                                   10340 ns        10286 ns        61062  
test_mode<int>/2048                                   31495 ns        31390 ns        23763  
test_mode<int>/4096                                   77137 ns        76821 ns         9569  
test_mode<int>/8192                                  172714 ns       170798 ns         3998  
[1]    46061 segmentation fault  ./reporting/performance/test_mode.x  
```  
  
Recompiled with `-fsanitize=address -fsanitize=undefined`:  
  
```  
 ./reporting/performance/test_mode.x  
2020-07-04 15:06:44  
Running ./reporting/performance/test_mode.x  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 262K (x2)  
  L3 Unified 3145K (x1)  
Load Average: 2.26, 3.20, 3.16  
=================================================================  
==46572==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x602000005ef8 at pc 0x000106455186 bp 0x7ffee97b1ba0 sp 0x7ffee97b1b98  
READ of size 4 at 0x602000005ef8 thread T0  
    #0 0x106455185 in std::vector<std::iterator_traits<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >::value_type, std::allocator<std::iterator_traits<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >::value_type> > boost::math::statistics::sorted_mode<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >(__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >, __gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >) univariate_statistics.hpp:542  
  
0x602000005ef8 is located 0 bytes to the right of 8-byte region [0x602000005ef0,0x602000005ef8)  
allocated by thread T0 here:  
    #0 0x106deec0f in wrap__Znwm (libasan.5.dylib:x86_64+0x7dc0f)  
    #1 0x106453bb1 in std::vector<std::iterator_traits<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >::value_type, std::allocator<std::iterator_traits<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >::value_type> > boost::math::statistics::sorted_mode<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >(__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >, __gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >) <Invalid path>  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow univariate_statistics.hpp:542 in std::vector<std::iterator_traits<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >::value_type, std::allocator<std::iterator_traits<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >::value_type> > boost::math::statistics::sorted_mode<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > > >(__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >, __gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >)  
Shadow bytes around the buggy address:  
  0x1c0400000b80: fa fa 00 fa fa fa 00 fa fa fa 00 fa fa fa 00 fa  
  0x1c0400000b90: fa fa 00 fa fa fa 00 fa fa fa 00 fa fa fa 00 fa  
  0x1c0400000ba0: fa fa 00 fa fa fa 00 fa fa fa 00 fa fa fa fd fd  
  0x1c0400000bb0: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
  0x1c0400000bc0: fa fa fd fd fa fa fd fa fa fa 00 00 fa fa 00 00  
=>0x1c0400000bd0: fa fa 00 fa fa fa 00 fa fa fa 00 fa fa fa 00[fa]  
  0x1c0400000be0: fa fa 04 fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000bf0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000c00: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000c10: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000c20: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07  
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
  Shadow gap:              cc  
==46572==ABORTING  
[1]    46572 abort      ./reporting/performance/test_mode.x  
```

---

## Review 16 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 19:13:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442626747  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 576 |+ 
 577 |+ template<class RandomAccessContainer>
 578 |+ inline auto sorted_mode(RandomAccessContainer & v)
```

> Username: NAThompson  
> Created_at: 2020-07-04 19:13:17 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449798122  

This can be marked `const`: `RandomAccessContainer const & v`.  
  
BTW does this require `RandomAccess` or can it be a `std::list`?

> Username: mborland  
> Created_at: 2020-07-05 17:20:57 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449899667  

Now that it is not sorting it can take a forward iterator instead of random access iterator


---

## Review 17 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 19:13:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442626768  

📁 reporting/performance/test_mode.cpp

```diff
   1 |+ //  (C) Copyright Nick Thompson 2020.
```

> Username: NAThompson  
> Created_at: 2020-07-04 19:13:49 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449798140  

Put your name in the copyright.


---

## Review 18 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 19:14:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442626822  

📁 reporting/performance/test_mode.cpp

```diff
 111 |+ }
 112 |+ 
 113 |+ BENCHMARK_TEMPLATE(test_mode, int)->RangeMultiplier(2)->Range(1<<1, 1<<22)->Complexity();
```

> Username: NAThompson  
> Created_at: 2020-07-04 19:14:55 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449798213  

On most systems `int` is the same as `int32_t`, so this benchmark is superfluous.


---

## Review 19 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 19:15:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442626845  

📁 test/univariate_statistics_test.cpp

```diff
 861 |+     BOOST_TEST(ref == test[0]);
 862 |+ 
 863 |+     // Does it work with a bi-modal distribuition?
```

> Username: NAThompson  
> Created_at: 2020-07-04 19:15:21 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449798265  

Nitpick: Spelling of distribution.


---

## Review 20 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-04 19:17:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442626961  

📁 test/univariate_statistics_test.cpp

```diff
 940 |+     test_mode<int32_t>();
 941 |+     test_mode<int64_t>();
 942 |+     test_mode<u_int32_t>();
```

> Username: NAThompson  
> Created_at: 2020-07-04 19:17:33 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449798412  

Is `u_int32_t` standard? I thought it was `uint32_t`.

> Username: NAThompson  
> Created_at: 2020-07-06 17:38:42 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450379374  

I think we should do `uint32_t` here.


---

## Comment 21

> Username: mborland  
> Created_at: 2020-07-05 17:44:32 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-653917720  

@NAThompson I am unable to replicate your seg fault. Ran with the same compiler flags on g++9, g++10, clang++10. I am not sure what the discrepancy is.

---

## Comment 22

> Username: NAThompson  
> Created_at: 2020-07-05 20:13:08 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-653933678  

@mborland : Did you compile with `-fsanitize=address -fsanitize=undefined`?

---

## Comment 23

> Username: mborland  
> Created_at: 2020-07-05 21:58:43 UTC  
> Updated_at: 2020-07-05 21:58:56 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-653944114  

@NAThompson I had not because I could not replicate the seg fault, but should have. You could de-reference last which is UB. Everything now shows a benchmark between 0.67 nlog(n) and 0.92n

---

## Comment 24

> Username: NAThompson  
> Created_at: 2020-07-05 22:48:46 UTC  
> Updated_at: 2020-07-05 22:55:43 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-653948596  

@mborland : With the new changes, I do not see any UB.  
  
Make sure to click "Resolve conversation" when you fix suggested changes.

---

## Review 25 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-05 22:49:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442712244  

📁 reporting/performance/test_mode.cpp

```diff
  15 |+     std::random_device rd;
  16 |+     std::mt19937_64 mt(rd());
  17 |+     std::uniform_real_distribution<double> dist {1, 10};
```

> Username: NAThompson  
> Created_at: 2020-07-05 22:49:44 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449927223  

This should be `std::uniform_int_distribution`.


---

## Review 26 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-05 22:50:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442712299  

📁 reporting/performance/test_mode.cpp

```diff
  24 |+     for (auto _ : state)
  25 |+     {
  26 |+         benchmark::DoNotOptimize(mode(v));
```

> Username: NAThompson  
> Created_at: 2020-07-05 22:50:28 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449927297  

I would only  benchmark `sorted_mode`, since the performance of `std::sort` is understood.


---

## Review 27 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-05 22:52:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442712405  

📁 test/univariate_statistics_test.cpp

```diff
 834 | }
 835 | 
 836 |+ template<class Real>
```

> Username: NAThompson  
> Created_at: 2020-07-05 22:52:10 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449927437  

Change `Real` to `Z`.


---

## Review 28 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-05 22:52:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-442712440  

📁 test/univariate_statistics_test.cpp

```diff
 837 |+ void test_mode()
 838 |+ {
 839 |+     std::vector<Real> v {1, 2, 2, 3, 4, 5};
```

> Username: NAThompson  
> Created_at: 2020-07-05 22:52:49 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r449927484  

Could you add a zero length vector and a length one vector to the tests?


---

## Comment 29

> Username: NAThompson  
> Created_at: 2020-07-06 12:58:33 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654218774  

@mborland : Might also want to add a test of `std::list` as well.

---

## Review 30 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-06 17:29:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443275396  

📁 reporting/performance/test_mode.cpp

```diff
 110 |+     state.SetComplexityN(state.range(0));
 111 |+ }
 112 |+ 
```

> Username: NAThompson  
> Created_at: 2020-07-06 17:29:03 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450374277  

Looks like you forgot to measure `test_mode` here, also `test_mode` is calling `mode` rather than `sorted_mode`.


---

## Review 31 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-06 17:37:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443280638  

📁 test/univariate_statistics_test.cpp

```diff
 860 |+     std::array<Z, 6> u {1, 2, 2, 3, 4, 5};
 861 |+     test = boost::math::statistics::mode(u);
 862 |+     BOOST_TEST(ref == test[0]);
```

> Username: NAThompson  
> Created_at: 2020-07-06 17:37:04 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450378531  

I would use `BOOST_TEST_EQ` here rather than `BOOST_TEST`; it'll give a nicer message if we ever break this.


---

## Comment 32

> Username: NAThompson  
> Created_at: 2020-07-06 17:40:38 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654374532  

@mborland : Could you update [univariate_statistics.qbk](https://github.com/boostorg/math/blob/develop/doc/statistics/univariate_statistics.qbk)?

---

## Comment 33

> Username: NAThompson  
> Created_at: 2020-07-06 17:46:58 UTC  
> Updated_at: 2020-07-06 17:48:11 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654377329  

Ok, ran `cppcheck` over the diffs; it's clean. Ran the benchmarks, got O(N) scaling except for `test_mode`, which oddly looks more like nlog(n), even when calling `sorted_mode` . Could you do a `vector::reserve` with (say) 16 elements and see if that increases the performance?

---

## Comment 34

> Username: mborland  
> Created_at: 2020-07-06 18:33:28 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654398768  

@NAThompson I am seeing O(N) scaling for test_mode. Where would you add the vector::reserve? It does not play nicely with std::generate.

---

## Review 35 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-06 18:54:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443330506  

📁 reporting/performance/test_mode.cpp

```diff
  21 |-     std::vector<Z> v(state.range(0));
  21 |+     std::vector<Z> v;
  22 |+     v.reserve(state.range(0));
```

> Username: NAThompson  
> Created_at: 2020-07-06 18:54:43 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450418460  

I meant call `v.reserve` in `sorted_mode`; I believe this is UB.


---

## Review 36 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-06 18:56:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443331870  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 520 |+     using Size = typename std::iterator_traits<ForwardIterator>::difference_type;
 521 |+ 
 522 |+     std::vector<Z> modes {};
```

> Username: NAThompson  
> Created_at: 2020-07-06 18:56:57 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450419580  

modes.reserve(16);


---

## Comment 37

> Username: NAThompson  
> Created_at: 2020-07-06 19:10:27 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654415377  

@mborland : Did that change make it faster?

---

## Comment 38

> Username: mborland  
> Created_at: 2020-07-06 19:18:03 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654418686  

@NAThompson It's a hair faster. Dropped from avg 2.8N to 2.75N for test_mode.

---

## Comment 39

> Username: NAThompson  
> Created_at: 2020-07-06 20:56:59 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654461145  

Ok, looks good. Will merge once the CI cycles.

---

## Review 40 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-06 23:51:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443475244  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 514 | 
 515 |+ template<class ForwardIterator>
 516 |+ auto sorted_mode(const ForwardIterator first, const ForwardIterator last) -> std::vector<typename std::iterator_traits<ForwardIterator>::value_type>
```

> Username: jeremy-murphy  
> Created_at: 2020-07-06 23:51:46 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450536841  

If you really want it to be generic, don't return a `std::vector`. Do as all the standard algorithms do: take an output iterator.

> Username: NAThompson  
> Created_at: 2020-07-07 01:29:12 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450561854  

@jeremy-murphy : Good suggestion; should it look like:  
  
```cpp  
std::vector<int> modes;  
mode(v.begin(), v.end(),  
              std::back_inserter(modes));  
```

> Username: mborland  
> Created_at: 2020-07-07 02:15:29 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450574016  

@NAThompson That is pretty idiomatic STL. Looking at the test_num benchmark it is nearly equivalent to returning the vector in terms of complexity. The one caveat is I can not use `benchmark::DoNotOptimize` because sorted_modes would no longer have a return value.

> Username: jeremy-murphy  
> Created_at: 2020-07-07 02:32:10 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450578212  

@mborland , looks closely at the STL algorithms that take output iterators, they _do_ have a return value.

> Username: mborland  
> Created_at: 2020-07-07 02:57:44 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450584406  

@jeremy-murphy Found them. It had been a bit since I cracked open the C++ Programming Language.


---

## Review 41 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-07 02:40:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443523989  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 535 |+         {
 536 |+             continue;
 537 |+         }
```

> Username: jeremy-murphy  
> Created_at: 2020-07-07 02:40:58 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450580330  

This will essentially do `find` on the input sequence twice. Yes, it's already in the cache, but it's not logical and not required. See my next comment down below about `previous_search`.


---

## Review 42 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-07 02:45:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443525137  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 557 |+         }
 558 |+ 
 559 |+         previous_search = *it;
```

> Username: jeremy-murphy  
> Created_at: 2020-07-07 02:45:04 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450581289  

Instead of using `previous_search`, do `it = end_it`. But actually, take it a step further: change the loop to `while (first != last)` and keep moving `first` forward with `first = end_it`.


---

## Review 43 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:19:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443568818  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 523 |+     modes.reserve(16);
 524 |+     Size max_counter {0};
 525 |+     auto it {first};
```

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:19:15 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450616490  

It's perfectly OK to use `first` as the variable that gets modified, you don't have to make a copy of it. That's generally how STL algorithms are implemented. `while (first != last)` was Stepanov's mantra.


---

## Review 44 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:26:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443571225  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 555 |+     }
 556 |+ 
 557 |+     if(max_counter == 1) // Return an empty vector if mode is undefined
```

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:26:42 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450618560  

This is not undefined, it's just GIGO. If the input is `{ 99 }` then the output is `{ 99 }`; if the input is `{1, 2, 3}` then the output is `{1, 2, 3}`, that's it.   
  
And once you remove this edge case for size 1, then you can _also_ remove the special case for `first == last` because the `std::move` will be a no-op in that case.


---

## Review 45 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:31:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-443572845  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 538 |+             ++current_count;
 539 |+             ++end_it;
 540 |+         }
```

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:31:42 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450620012  

You don't have to increment `current_count` on every iteration of this loop. You can just loop until the condition is met and then `current_count += std::distance(it, end_it)`.  
  
And then, you'll see that this loop is just `find_if` with a predicate for 'is not equal to `*it`'.

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:34:04 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r450620592  

Ahh, actually, if the input sequence is not random access, then `std::distance` will be linear. Hmm. Maybe best to leave it as is after all. :)


---

## Comment 46

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:45:34 UTC  
> Updated_at: 2020-07-07 05:46:07 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654616380  

Here is a deeper issue for you: what are the concept requirements on the input elements for statistical mode? I would say they have to have an equivalence relationship (comparable by `==`), but not much else. Specifically, objects without ordering (comparable by `<`) can still have a mode calculated for them.  
  
So, there is an uneasy mixing of sorting and ordering with mode here. It might be suitable for _special cases_ of input (i.e., integers), but it doesn't work in the general case. In the general case, the input needs to be partitioned into equivalence classes, perhaps like this: http://doi.org/10.1007/978-3-319-21840-3_36    
  
I know, mode probably looked like a simple algorithm on the surface.  ;)

---

## Comment 47

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:52:53 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654618595  

Ah, that previous paper was actually in aid of an algorithm to find mode using equality comparisons: https://link.springer.com/chapter/10.1007/978-3-319-30139-6_28

---

## Comment 48

> Username: jeremy-murphy  
> Created_at: 2020-07-07 05:53:50 UTC  
> Updated_at: 2020-07-07 06:02:03 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654618921  

And a slightly newer one, which refers to modes, not just _the_ mode: https://www.sciencedirect.com/science/article/pii/S030439751730662X

---

## Comment 49

> Username: jeremy-murphy  
> Created_at: 2020-07-07 06:31:10 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654631661  

Anyway, if you don't have access to, or interest in, these papers on equivalence-class sorting that's perfectly OK. It shouldn't stop this code going through, which is useful for ordered types. It's just worth being aware of the whole landscape of mode algorithms.

---

## Comment 50

> Username: mborland  
> Created_at: 2020-07-07 16:11:13 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-654968719  

@jeremy-murphy Unfortunately those papers are behind a paywall, and I do not have enterprise access. I appreciate the insight.

---

## Review 51 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-08 02:34:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-444372241  

📁 include/boost/math/statistics/univariate_statistics.hpp

```diff
 521 |+ 
 522 |+     std::vector<Z> modes {};
 523 |+     modes.reserve(16);
```

> Username: jeremy-murphy  
> Created_at: 2020-07-08 02:34:32 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r451247723  

A performance idea, and honestly you're under no obligation to pursue it. You can just ignore me. But did you consider trying `boost::container::small_vector` here? Because I assume _most_ of the time, `modes` is not going to grow large at all. Or it might not make a difference. Entirely up to you.

> Username: NAThompson  
> Created_at: 2020-07-08 02:37:31 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r451248457  

@jeremy-murphy : It would couple `boost::container` into `boost::math`.

> Username: mborland  
> Created_at: 2020-07-08 02:48:59 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r451251322  

@jeremy-murphy By calling `std::vector::reserve()` and using `std::vector::emplace_back()` dynamic memory allocation is avoided in most cases, which from the docs looks like what `boost::container::small_vector` accomplishes.

> Username: jeremy-murphy  
> Created_at: 2020-07-08 03:14:36 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r451257757  

OK, good, just a thought.  :)


---

## Comment 52

> Username: jeremy-murphy  
> Created_at: 2020-07-08 03:31:41 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-655261304  

Btw, did you consider the performance optimization of breaking the loop early if `max_count` > `last - it`?  
In other words, if you can't find an equally large (or larger) mode in the remaining input data, then you don't need to process it.  
  
And the algorithm wouldn't have to check on every iteration, it could just move `last` backwards each time it finds a new value for `max_count`. Of course that doesn't work for `ForwardIterator`, but then that's the disadvantage of such iterators.

---

## Comment 53

> Username: mborland  
> Created_at: 2020-07-08 18:58:42 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-655697623  

@jeremy-murphy I just compared the bench mark of the current configuration to an edited version that shifts `last` backwards as you suggested. Generally they are comparable but in the case of `sequential_multiple_test_mode` the performance dropped from O(N) to O(NlgN).

---

## Comment 54

> Username: jeremy-murphy  
> Created_at: 2020-07-09 04:07:28 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-655885923  

Ah, that's a pity about `sequential_multiple_test_mode`. Presumably it would have the most positive effect when that sequence is reversed, with the largest sequence at the front. Thanks for testing it!

---

## Review 55 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-09 11:41:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-445536051  

📁 doc/statistics/univariate_statistics.qbk

```diff
 281 |+ If your data is sorted, the following function can be used instead:
 282 |+ 
 283 |+     std::vector<int> v {1, 3, 2, 2, 5, 4};
```

> Username: NAThompson  
> Created_at: 2020-07-09 11:41:37 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r452155816  

Note that this data is not sorted.

> Username: NAThompson  
> Created_at: 2020-07-09 11:43:02 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r452156507  

Might want to note that passing an unsorted list to `sorted_mode` is a bug.


---

## Review 56 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-09 11:41:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-445536225  

📁 doc/statistics/univariate_statistics.qbk

```diff
 286 |+     // Mode is 2, modes.size() == 1
 287 |+     std::deque<int> d_modes;
 288 |+     std::array<int, 7> w {2, 2, 3, 1, 5, 4, 4};
```

> Username: NAThompson  
> Created_at: 2020-07-09 11:41:52 UTC  
> Updated_at: 2020-07-09 17:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r452155946  

This data is not sorted.


---

## Comment 57

> Username: NAThompson  
> Created_at: 2020-07-09 11:45:17 UTC  
> Url: https://github.com/boostorg/math/pull/390#issuecomment-656079655  

The two build errors are known and are from boost::serialization. Modulo the two trivial issues I found in the docs, I think this looks good.  
  
@jeremy-murphy : Your suggestions were invaluable; I think this now is a very high quality implementation.  
  
@mborland : Nice work, will merge once the doc diffs are in.

---

## Review 58 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-10 01:24:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/390#pullrequestreview-446066415  

📁 doc/statistics/univariate_statistics.qbk

```diff
 292 |+ /Nota bene/: The requirements for sorted_mode are reduced to forward iterators because there is no call to `std::sort`.
 293 |+ 
 294 |+ /Nota bene/: Passing unsorted data to sorted_mode is a bug.
```

> Username: jeremy-murphy  
> Created_at: 2020-07-10 01:24:45 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r452573842  

That's not strictly true, right? It doesn't have to be sorted, it just has to have equal values be consecutive, which is the same requirement as for `std::unique`. Sorting is just the simplest way to achieve it.

> Username: mborland  
> Created_at: 2020-07-10 02:17:29 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r452586384  

@jeremy-murphy You are correct. Consecutive equal values would work here.

> Username: NAThompson  
> Created_at: 2020-07-10 02:28:48 UTC  
> Updated_at: 2020-07-10 02:29:07 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r452589167  

I would probably describe this in a literary sense as a "distracting caveat"; not all true statements about the code need to be documented.  
  
But I think having use cases would clarify whether this needs to be explicitly stated; do we have any? I personally have never needed to compute a mode.

> Username: mborland  
> Created_at: 2020-07-10 03:19:05 UTC  
> Updated_at: 2020-07-10 03:19:16 UTC  
> Url: https://github.com/boostorg/math/pull/390#discussion_r452600791  

@NAThompson I can't think of a case where data being handled by sorted_mode would be configured differently than what you would get from `std::sort`


---
