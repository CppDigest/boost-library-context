# #506 Define BOOST_NO_CXX20_HDR_RANGES for libc++ < 18 [Closed]

> Username: Flamefire  
> Created at: 2024-11-10 13:19:17 UTC  
> Updated at: 2024-12-09 10:27:09 UTC  
> Closed at: 2024-12-09 10:27:08 UTC  
> Url: https://github.com/boostorg/config/pull/506  

At least for Clang the `-fexperimental-library` flag is required to enable full range support.  
Otherwise e.g. `std::ranges::join_view` won't be available.  
  
See https://godbolt.org/z/bMdaYM56h

---
