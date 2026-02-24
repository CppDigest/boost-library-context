# #2902 Fix friend declarations of asio::associator<> [Merged]

> Username: ashtum  
> Created at: 2024-07-10 10:20:13 UTC  
> Updated at: 2024-07-10 11:54:59 UTC  
> Merged at: 2024-07-10 11:54:59 UTC  
> Closed at: 2024-07-10 11:54:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2902  

`asio::associator<>` has got an extra template parameter that enables SFINAE-based partial specialization:  
https://github.com/chriskohlhoff/asio/commit/4928948a6e607664ee0ab5590544def762a118ad"

---
