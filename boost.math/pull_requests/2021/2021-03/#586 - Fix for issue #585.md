# #586 Fix for issue #585 [Merged]

> Username: mborland  
> Created at: 2021-03-28 13:44:40 UTC  
> Updated at: 2021-04-06 17:18:19 UTC  
> Merged at: 2021-04-06 17:16:23 UTC  
> Closed at: 2021-04-06 17:16:23 UTC  
> Url: https://github.com/boostorg/math/pull/586  

Provides thread synchronization if using the MSVC implementation of the Parallel TS.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-03-28 14:49:53 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-808907257  

What's the performance implications of this diff? Could you run the benchmarks?

---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-28 15:12:11 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-808910084  

I expect that the performance penalty will be high maybe to the point where it is better off just running the sequential method. I have been meaning to create a MSVC environment; should have benchmarks up in the next day or two.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-03-28 17:45:23 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-808931026  

After a minor correction to the lambda, this still fails I'm afraid :(

---

## Comment 4

> Username: mborland  
> Created_at: 2021-03-28 18:03:57 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-808933864  

> After a minor correction to the lambda, this still fails I'm afraid :(  
  
OK. I will get MSVC up and running on my end to troubleshoot and re-benchmark.

---

## Comment 5

> Username: mborland  
> Created_at: 2021-03-31 19:15:44 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-811363352  

The new implementation takes the decomposition method from the first four moments calculation. This maintains the required sorting. Clean on tsan with both Clang 12 and GCC 11. New benchmarks:  
  
```  
gini_coefficient<double>/real_time_BigO                   1.09 N          1.08 N      
gini_coefficient<double>/real_time_RMS                      13 %            13 %  
parallel_gini_coefficient<double>/real_time_BigO          0.88 N          0.82 N      
parallel_gini_coefficient<double>/real_time_RMS             36 %            39 %  
```

---

## Comment 6

> Username: mborland  
> Created_at: 2021-04-05 18:20:44 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-813555918  

Any changes for this one? The only CI failure is from CircleCI from before the config was committed.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-04-06 07:31:05 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-813896143  

Looks good to me, @NAThompson ?

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-04-06 13:19:15 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-814113336  

I think this is now *correct*, but I'm not sure the parallelism is worth it; it seems the speedup  is not so impressive.  
  
What about using the parallel sort from the standard library? If you use that first, then you can reason much more clearly about a parallel computation of the Gini coefficient.  
  
(Note: This is just a suggestion. I believe this PR can be merged if @mborland desires; we don't need to make every PR a research program.)

---

## Comment 9

> Username: mborland  
> Created_at: 2021-04-06 17:15:06 UTC  
> Url: https://github.com/boostorg/math/pull/586#issuecomment-814289994  

> I think this is now _correct_, but I'm not sure the parallelism is worth it; it seems the speedup is not so impressive.  
>   
> What about using the parallel sort from the standard library? If you use that first, then you can reason much more clearly about a parallel computation of the Gini coefficient.  
  
Passing an execution policy to `std::is_sorted` and `std::sort` is [used here.](https://github.com/boostorg/math/blob/1924e3179c24ac4d73181587d752774ad72c0ccb/include/boost/math/statistics/univariate_statistics.hpp#L416-L424) I agree the speedup is not impressive. Another interface could be added where a distribution function is taken as input to directly calculate the gini coefficient (much like is done to compare against a uniform distribution in the test cases). I do not know if implementing all of those would be value added.

---
