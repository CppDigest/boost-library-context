# #372 - Why is src.cpp exists in asio, which should be header-only [Closed]

> Username: qh-huang  
> Created at: 2021-01-14 17:06:58 UTC  
> Updated at: 2021-01-14 22:15:19 UTC  
> Closed at: 2021-01-14 22:15:18 UTC  
> Url: https://github.com/boostorg/asio/issues/372  

https://github.com/boostorg/asio/blob/933e9373d2814c7e823c32cd6c242eb7222906c9/include/boost/asio/impl/src.cpp#L2  
  
As title, I'm confused about it. All Asio files should be headers, right?

---

## Comment 1

> Username: ghost  
> Created at: 2021-01-14 22:15:17 UTC  
> Url: https://github.com/boostorg/asio/issues/372#issuecomment-760509999  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#777](https://github.com/chriskohlhoff/asio/issues/777).
