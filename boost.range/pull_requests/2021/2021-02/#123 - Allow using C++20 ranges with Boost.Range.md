# #123 Allow using C++20 ranges with Boost.Range [Open]

> Username: ecatmur  
> Created at: 2021-02-18 20:08:33 UTC  
> Updated at: 2021-02-18 20:08:33 UTC  
> Url: https://github.com/boostorg/range/pull/123  

e.g.:  
  
    std::views::iota(99) | std::views::common | boost::adaptors::indexed(42)  
  
obviously this won't work with heterogeneous ranges, so i've constrained to common ranges.  
  
Demo: https://godbolt.org/z/n53jWz

---
