# #428 Fix type in namespace of ssl/stream example [Open]

> Username: melroy89  
> Created at: 2023-12-26 04:10:54 UTC  
> Updated at: 2023-12-26 04:10:54 UTC  
> Url: https://github.com/boostorg/asio/pull/428  

Fix a missing colon symbol as well as `boost::` in the asio/ssl/stream.hpp example.  
  
Ps. I'm also not sure about the default context should that not be a client context (eg. `boost::asio::ssl::context::tlsv12_client`). But I digress...  
  
The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
  
Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---
