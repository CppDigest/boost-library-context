# #163 Add network_v4 and network_v6 includes to asio.hpp [Closed]

> Username: MarkusKoehlerAI  
> Created at: 2018-11-07 10:19:24 UTC  
> Updated at: 2020-12-30 01:48:08 UTC  
> Closed at: 2020-12-30 01:48:08 UTC  
> Url: https://github.com/boostorg/asio/pull/163  

The documentation lists the _asio.hpp_ as convenience headers for the _ip::network_v4_ and _ip::network_v6_ classes, but the header actually never included them

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:48:08 UTC  
> Url: https://github.com/boostorg/asio/pull/163#issuecomment-752298778  

Fixed in asio 1.14.0 / boost 1.70.

---
