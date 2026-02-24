# #26 - Warning about implicit copy assignment operator in `handle_type` [Closed]

> Username: burnpanck  
> Created at: 2019-12-13 20:47:21 UTC  
> Updated at: 2024-11-09 13:52:32 UTC  
> Closed at: 2024-11-09 13:52:20 UTC  
> Url: https://github.com/boostorg/heap/issues/26  

Starting with GCC 9 (though not with GCC 8, nor Clang 9), I get a warning when I assign a mutation handle (`-Wdeprecated-copy`). The reason seems to be that, because `struct handle_type` declares a copy constructor ([`boost/heap/detail/mutable_heap.hpp:94`](https://github.com/boostorg/heap/blob/19fda03545e1b85b3a2d3a94b9a3930ec64335d4/include/boost/heap/detail/mutable_heap.hpp#L94)) but not an assignment operator, GCC warns that the implicitly declared one might not be intentionally there.  
  
I assume that in this case, the warning is unnecessary, as the explicit copy constructor really is in fact not any different from an implicit one. Probably, it just needs an accompanying copy assignment operator, possibly as `handle_type operator=(handle_type const &) = default;` if that is supported by all target compilers.

---

## Comment 1

> Username: timblechmann  
> Created at: 2024-11-09 13:52:20 UTC  
> Updated at: 2024-11-09 13:52:32 UTC  
> Url: https://github.com/boostorg/heap/issues/26#issuecomment-2466224913  

should be fixed in develop
