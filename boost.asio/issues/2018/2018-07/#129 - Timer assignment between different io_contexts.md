# #129 - Timer assignment between different io_contexts [Closed]

> Username: djarek  
> Created at: 2018-07-11 21:00:41 UTC  
> Updated at: 2020-12-30 00:52:16 UTC  
> Closed at: 2020-12-30 00:52:15 UTC  
> Url: https://github.com/boostorg/asio/issues/129  

Is this move assignment allowed, assuming ctx1 and ctx2 each have 1 or more threads running in them?  
```  
io_context ctx1, ctx2;  
steady_timer t1{ctx1}, t2{ctx2};  
  
t1 = std::move(t2);

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:52:14 UTC  
> Url: https://github.com/boostorg/asio/issues/129#issuecomment-752289711  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#628](https://github.com/chriskohlhoff/asio/issues/628).
