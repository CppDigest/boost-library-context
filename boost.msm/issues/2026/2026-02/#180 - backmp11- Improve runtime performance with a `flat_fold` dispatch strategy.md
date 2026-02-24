# #180 - backmp11: Improve runtime performance with a `flat_fold` dispatch strategy [Closed]

> Username: chandryan  
> Created at: 2026-02-14 11:15:50 UTC  
> Updated at: 2026-02-22 11:29:19 UTC  
> Closed at: 2026-02-22 11:29:19 UTC  
> Url: https://github.com/boostorg/msm/issues/180  

An adjustment of the [fsm-benchmarks](https://github.com/chandryan/fsm-benchmarks) to get more realistic benchmark results revealed some interesting details between MSM(backmp11) and SML:  
  
In the new benchmark compile time and RAM consumption of both libraries are closer to each other than what the previous benchmark suggested, and in the large SM benchmark SML is faster.  
  
Changing the dispatch strategy in SML to `jump_table` (similar to what backmp11 uses?) has a negative impact on SML's runtime performance and makes it about as fast as backmp11.  
This suggests that it's worth taking a look into alternative dispatch strategies in MSM.

---

## Comment 1

> Username: chandryan  
> Created at: 2026-02-22 10:24:35 UTC  
> Updated at: 2026-02-22 10:36:35 UTC  
> Url: https://github.com/boostorg/msm/issues/180#issuecomment-3940659142  

Result of investigation:  
A fold expression offers significant improvements in runtime performance compared to the current approach, even in binary size.  
It's called `flat_fold` to distinguish it from a binary fold expression, which was also evaluted but was not useful (probably due to semantically enforced sequential processing of the transitions in the dispatch table).  
  
However, theoretically the current approach (now called `function_pointer_array`) guarantees O(1) dispatch, while a `flat_fold` relies on compiler optimizations to make it efficient. There might be use cases where a function pointer table is favorable, though for the majority of cases `flat_fold` is the better choice.  
  
Enhancements targeted for `backmp11`:  
  
Introduce dispatch strategies for `favor_runtime_speed`, with `flat_fold` being the default and the previous approach `function_pointer_array` an alternative.
