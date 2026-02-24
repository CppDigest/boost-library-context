# #114 - include benchmark code for find_*_of algorithm [Closed]

> Username: sehe  
> Created at: 2022-05-02 15:42:46 UTC  
> Updated at: 2025-12-14 20:15:07 UTC  
> Closed at: 2025-12-14 20:15:07 UTC  
> Url: https://github.com/boostorg/core/issues/114  

`boost::core::string_view` comes with a very elegant optimized `find_{first,last}{,_not}_of` implementation.  
  
I wonder in how far the runtime performance depends on available compiler optimizations. It would be nice to have the benchmark code included into the repository so there is a reference people can use to check with new toolchains/configs.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-12-14 19:53:51 UTC  
> Url: https://github.com/boostorg/core/issues/114#issuecomment-3651961783  

Since the subject of `boost::core::string_view` came up on the ML, it reminded me that the benchmarks have been sitting on my drive since forever and I was repeatedly forgetting to add them to the repo.  
  
So I have now, https://github.com/boostorg/core/commit/b7fdbf41a7523e872960b56108c26db1cb1de692.  
  
Interestingly, MSFT hasn't stood still and the `std::string_view` from VS2022 has AVX2 vectorized implementations of these functions, with the end result being almost twice as fast as `boost::core::string_view`.
