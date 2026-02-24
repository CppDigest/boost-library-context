# #269 - allocators, use_awaitable, and co_spawn [Closed]

> Username: cedral  
> Created at: 2019-08-12 16:42:17 UTC  
> Updated at: 2020-12-30 01:08:13 UTC  
> Closed at: 2020-12-30 01:08:12 UTC  
> Url: https://github.com/boostorg/asio/issues/269  

the old use_future thing allowed setting an allocator but use_awaitable does not appear to do that. I am associating an allocator with my call to co_spawn but it is not clear to me if that is a enough. I would appreciate any insight that could be provided on that.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:08:11 UTC  
> Url: https://github.com/boostorg/asio/issues/269#issuecomment-752292734  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#711](https://github.com/chriskohlhoff/asio/issues/711).
