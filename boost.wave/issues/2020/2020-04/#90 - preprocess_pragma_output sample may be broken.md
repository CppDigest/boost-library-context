# #90 - preprocess_pragma_output sample may be broken [Closed]

> Username: jefftrull  
> Created at: 2020-04-28 16:33:54 UTC  
> Updated at: 2020-09-25 14:45:16 UTC  
> Closed at: 2020-09-25 14:45:16 UTC  
> Url: https://github.com/boostorg/wave/issues/90  

Clang gives a warning for [this code](https://github.com/boostorg/wave/blob/2e8eeeeb4d30e22919dbfa057f09e470d04d5863/samples/preprocess_pragma_output/preprocess_pragma_output.hpp#L146), saying:  
  
> variable 'it' is incremented both in the loop header and in the loop body [-Wfor-loop-analysis]  
  
which does seem to be the case. It's unclear whether that was by design and should probably be investigated.

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-05-19 22:04:48 UTC  
> Url: https://github.com/boostorg/wave/issues/90#issuecomment-631108427  

@hkaiser AFAICT this second increment serves the function of skipping whitespace... does that seem right to you? If so I'll add a comment and a clang-tidy suppression.
