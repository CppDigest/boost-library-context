# #41 Use enable_current_exception instead of wrapexcept in copy_exception [Merged]

> Username: pdimov  
> Created at: 2022-01-25 17:59:32 UTC  
> Updated at: 2023-08-05 17:46:03 UTC  
> Merged at: 2022-01-26 03:39:39 UTC  
> Closed at: 2022-01-26 03:39:39 UTC  
> Url: https://github.com/boostorg/exception/pull/41  

Since `wrapexcept` is an undocumented implementation detail of `throw_exception`, it's better to not use it here. As `enable_current_exception` on `throw` is no longer necessary under C++11 as `boost::exception_ptr` automatically uses `std::exception_ptr` when present (https://godbolt.org/z/GPK99jTzY), it's possible to remove this functionality from `wrapexcept` under C++11 to reduce the codegen footprint of using `boost::throw_exception`, and this PR makes sure that `copy_exception` will not be broken by such a future change to `wrapexcept`.

---
