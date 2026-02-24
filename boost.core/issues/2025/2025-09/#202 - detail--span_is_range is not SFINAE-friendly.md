# #202 - detail::span_is_range is not SFINAE-friendly [Closed]

> Username: ashtum  
> Created at: 2025-09-08 14:42:59 UTC  
> Updated at: 2025-10-24 08:36:06 UTC  
> Closed at: 2025-10-24 08:36:06 UTC  
> Url: https://github.com/boostorg/core/issues/202  

Calling span with a single element that partially matches with `span(R&& r)` leads to a compile error: https://godbolt.org/z/hzfcdd8f6  
  
https://github.com/boostorg/core/blob/c6b098d25d32ea5f53950492c19904143bf3e6f1/include/boost/core/span.hpp#L227
