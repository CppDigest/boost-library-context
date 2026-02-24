# #333 Fix strand move constructor strand(strand<OtherExecutor>&& other) [Closed]

> Username: Erlkoenig90  
> Created at: 2020-03-24 16:33:10 UTC  
> Updated at: 2020-12-30 01:27:03 UTC  
> Closed at: 2020-12-30 01:27:03 UTC  
> Url: https://github.com/boostorg/asio/pull/333  

The `strand::strand(strand<OtherExecutor>&& other)` move constructor is broken as it tries to do an invalid cast on the wrong object. A `friend` declaration is also required to access the private members of the other `strand`. This PR should fix that.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:27:03 UTC  
> Url: https://github.com/boostorg/asio/pull/333#issuecomment-752295726  

Fixed in asio 1.16.1 / boost 1.73.

---
