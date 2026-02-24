# #56 - Forward declarations of std types cause compilation errors on some platforms [Closed]

> Username: igaztanaga  
> Created at: 2024-10-19 21:17:11 UTC  
> Updated at: 2024-10-19 21:19:39 UTC  
> Closed at: 2024-10-19 21:19:39 UTC  
> Url: https://github.com/boostorg/move/issues/56  

Main summary: _When compiling for a well-known console platform using C++20, the standard library implementation uses an inline namespace for versioning. This conflicts with the forward declarations in [boost/container/detail/std_fwd.hpp](https://github.com/boostorg/container/blob/develop/include/boost/container/detail/std_fwd.hpp) and results in names being ambiguous (e.g. std::less vs std::some_inline_namespace::less)._  
  
See issue https://github.com/boostorg/container/issues/292 for details.
