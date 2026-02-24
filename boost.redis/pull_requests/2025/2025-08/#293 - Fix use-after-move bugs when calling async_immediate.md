# #293 Fix use-after-move bugs when calling async_immediate [Closed]

> Username: ashtum  
> Created at: 2025-08-09 10:11:03 UTC  
> Updated at: 2025-08-09 16:57:42 UTC  
> Closed at: 2025-08-09 16:57:42 UTC  
> Url: https://github.com/boostorg/redis/pull/293  

@anarthal, There might be a similar issue in Boost.Mysql.

---

## Comment 1

> Username: ashtum  
> Created_at: 2025-08-09 10:39:09 UTC  
> Updated_at: 2025-08-09 11:01:13 UTC  
> Url: https://github.com/boostorg/redis/pull/293#issuecomment-3170587017  

Or maybe this isn’t a bug. From the implementation, it seems the `detail::initiate_immediate` constructor is called before the move occurs:  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/immediate.hpp#L94-L95  
I mean usages like this are definitely a bug:  
```C++  
asio::async_immediate(  
    self.get_io_executor(),  
    asio::append(std::move(self), ec));  
```  
But it seems the following is properly handled by the implementation:  
```C++  
asio::async_immediate(self.get_io_executor(), std::move(self));  
```  
  
  
**P.S.** Yeah, your usage is definitely fine. I just panicked because we’re close to the final release.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-08-09 16:47:31 UTC  
> Url: https://github.com/boostorg/redis/pull/293#issuecomment-3171913905  

Yes, the problem is due to the inner call being executed before self.get_io_executor(). This usage should be fine.

---

## Comment 3

> Username: ashtum  
> Created_at: 2025-08-09 16:54:12 UTC  
> Url: https://github.com/boostorg/redis/pull/293#issuecomment-3171917763  

That `asio::append(std::move(self), ...)` (and similar utilities) makes for a footgun in composed operations, it can happen in other operations like `async_read_some` too.

---
