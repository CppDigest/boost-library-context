# #913 - make detail::url_impl smaller [Closed]

> Username: vinniefalco  
> Created at: 2025-05-27 15:17:58 UTC  
> Updated at: 2026-01-28 19:15:07 UTC  
> Closed at: 2026-01-28 19:15:07 UTC  
> Url: https://github.com/boostorg/url/issues/913  

Currently, `detail::url_impl` uses `std::size_t`. In 64-bit builds this means 8 bytes per offset or size, which seems rather excessive. We should explore making this `std::uint32_t`.
