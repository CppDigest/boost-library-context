# #766 Switch to `boost::core::invoke_swap` [Merged]

> Username: Lastique  
> Created at: 2023-09-02 17:40:57 UTC  
> Updated at: 2023-10-08 23:35:41 UTC  
> Merged at: 2023-10-08 23:07:47 UTC  
> Closed at: 2023-10-08 23:07:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/766  

`boost::swap` is deprecated and will be removed. Use `boost::core::invoke_swap` as a replacement, where needed.  
  
Removed `using boost::swap` in `common.hpp`, since this is not the intended usage of Boost.Swap, and doing so did not add anything to the existing code (which already did `using std::swap`).  
  
Corrected documentation re. compatibility with `boost::swap`/`boost::core::invoke_swap`. It does not (and never did) use member `swap()` functions, but simply forwards to either `std::swap` or the user-defined free function overload (which, in turn, may forward to the member `swap()`).

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-09-02 21:31:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/766#issuecomment-1703946080  

The errors caused by undefined `std::ios_base` should be fixed by #767.

---
