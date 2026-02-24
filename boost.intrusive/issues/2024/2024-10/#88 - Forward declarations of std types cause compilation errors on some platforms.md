# #88 - Forward declarations of std types cause compilation errors on some platforms [Closed]

> Username: Botbail  
> Created at: 2024-10-16 16:46:47 UTC  
> Updated at: 2024-12-21 22:55:17 UTC  
> Closed at: 2024-12-21 22:55:16 UTC  
> Url: https://github.com/boostorg/intrusive/issues/88  

When compiling for a well-known console platform using C++20, the standard library implementation uses an inline namespace for versioning. This conflicts with the forward declarations in [boost/intrusive/detail/std_fwd.hpp](https://github.com/boostorg/intrusive/blob/develop/include/boost/intrusive/detail/std_fwd.hpp) and results in names being ambiguous (e.g. `std::less` vs `std::some_inline_namespace::less`).  
  
I believe Boost is at fault here because forward declaring `std` types is not allowed; perhaps this is one reason why. Although it may hurt compile times, in order to use Boost on this platform it's necessary to include the real headers rather than forward declaring. This behaviour should be removed, or at least made optional.  
  
Note: this is the same issue as [this one](https://github.com/boostorg/container/issues/292) in the container library.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-12-21 22:55:16 UTC  
> Url: https://github.com/boostorg/intrusive/issues/88#issuecomment-2558261467  

This bug was fixed with commit https://github.com/boostorg/move/commit/18b0f7e1813182da53690c697c0d4762efbf62ad in Boost.Move
