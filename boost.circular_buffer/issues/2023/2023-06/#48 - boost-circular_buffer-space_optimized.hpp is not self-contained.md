# #48 - boost/circular_buffer/space_optimized.hpp is not self-contained [Open]

> Username: reddwarf69  
> Created at: 2023-06-15 16:52:15 UTC  
> Updated at: 2023-06-15 16:52:15 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/48  

As can be seen in https://godbolt.org/z/5aabcr99d, `boost/circular_buffer/space_optimized.hpp` is not self-contained and including (only) it causes a compilation error.
