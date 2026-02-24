# #403 - Test failure due to span assertion [Closed]

> Username: anarthal  
> Created at: 2025-01-24 10:43:23 UTC  
> Updated at: 2025-01-24 14:44:17 UTC  
> Closed at: 2025-01-24 14:44:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/403  

There is a test accessing the 4th element of a std::array<T, 4> via a boost::span of 3 elements. This has worked because the 4th element does exist, but shouldn't use the span, but the array directly. boost::span now performs range checks, making tests fail.
