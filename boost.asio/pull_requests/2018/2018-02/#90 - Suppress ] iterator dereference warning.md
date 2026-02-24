# #90 Suppress [[nodiscard]] iterator dereference warning [Closed]

> Username: cstratopoulos  
> Created at: 2018-02-21 16:26:29 UTC  
> Updated at: 2018-05-05 18:58:02 UTC  
> Closed at: 2018-05-05 18:58:02 UTC  
> Url: https://github.com/boostorg/asio/pull/90  

The call operator in `buffer_debug_check` dereferences its member `Iterator` object, which can raise a warning when `Iterator` is, e.g., a `std::vector` iterator whose dereference operator is marked `[[nodiscard]]`. In MSVC using `/std:c++17` this raises warning C4834. This PR silences the warning by using `boost::ignore_unused`.

---

## Comment 1

> Username: egorpugin  
> Created_at: 2018-03-09 11:43:33 UTC  
> Url: https://github.com/boostorg/asio/pull/90#issuecomment-371790759  

+1

---

## Comment 2

> Username: poelmanc  
> Created_at: 2018-05-04 21:17:07 UTC  
> Url: https://github.com/boostorg/asio/pull/90#issuecomment-386737571  

We've seen the same problem. Adding boost::ignore_unused(*iter_) per the patch fixed it.

---
