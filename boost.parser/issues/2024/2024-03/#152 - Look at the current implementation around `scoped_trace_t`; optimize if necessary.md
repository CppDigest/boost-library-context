# #152 - Look at the current implementation around `scoped_trace_t`; optimize if necessary [Closed]

> Username: tzlaine  
> Created at: 2024-03-01 03:23:51 UTC  
> Updated at: 2024-03-23 23:17:42 UTC  
> Closed at: 2024-03-23 23:17:42 UTC  
> Url: https://github.com/boostorg/parser/issues/152  

From Peter Dimov in the Boost review:  
  
> the top function becomes the constructor of scoped_trace_t. (It's probably  
> not getting inlined.)  
>   
> Looks like the tracing functionality costs a lot even when off.  
  
If this is still the case, the flag that carries the information for whether or not to trace may need to become a template parameter.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-01 04:20:40 UTC  
> Updated at: 2024-03-01 04:21:10 UTC  
> Url: https://github.com/boostorg/parser/issues/152#issuecomment-1972480076  

This appears not to be necessary (see the commit on the branch above).  I modified `scoped_trace_t` to be empty when weare doing a no-trace parse, but it made no difference in my perf tests.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-03-02 21:28:23 UTC  
> Url: https://github.com/boostorg/parser/issues/152#issuecomment-1974913977  

The template change *was* in fact necessary.
