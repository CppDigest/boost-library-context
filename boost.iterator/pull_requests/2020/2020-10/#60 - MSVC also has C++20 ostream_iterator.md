# #60 MSVC also has C++20 ostream_iterator [Merged]

> Username: CaseyCarter  
> Created at: 2020-10-14 02:23:59 UTC  
> Updated at: 2020-10-14 15:18:06 UTC  
> Merged at: 2020-10-14 09:15:57 UTC  
> Closed at: 2020-10-14 09:15:57 UTC  
> Url: https://github.com/boostorg/iterator/pull/60  

Tell iterator_traits_test not to fail on MSVC when `std::output_iterator` has `difference_type` of `std::ptrdiff_t`.

---
