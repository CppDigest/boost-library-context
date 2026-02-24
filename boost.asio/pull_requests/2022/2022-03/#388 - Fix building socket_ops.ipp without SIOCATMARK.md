# #388 Fix building socket_ops.ipp without SIOCATMARK [Open]

> Username: ein-shved  
> Created at: 2022-03-02 15:46:24 UTC  
> Updated at: 2022-03-02 15:46:25 UTC  
> Url: https://github.com/boostorg/asio/pull/388  

Failed to build asio for systems without SIOCATMARK defined because of invalid old-version usage of undefined result variable in sockatmark  
  
The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
  
Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---
