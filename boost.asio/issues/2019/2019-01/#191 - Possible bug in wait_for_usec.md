# #191 - Possible bug in wait_for_usec? [Closed]

> Username: FoxBatius  
> Created at: 2019-01-08 11:51:43 UTC  
> Updated at: 2020-12-30 00:59:08 UTC  
> Closed at: 2020-12-30 00:59:06 UTC  
> Url: https://github.com/boostorg/asio/issues/191  

I haven't looked at this for more than a few minutes, but I find it odd that while the seconds delta is added, while calculating the stop time, the nanoseconds member is overwritten.  
  
https://github.com/boostorg/asio/blob/a6008b6427ddfc3222f83c53030c34c802c53b59/include/boost/asio/detail/posix_event.hpp#L133  
  
Shouldn't that be += as well?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:59:05 UTC  
> Url: https://github.com/boostorg/asio/issues/191#issuecomment-752291017  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#666](https://github.com/chriskohlhoff/asio/issues/666).
