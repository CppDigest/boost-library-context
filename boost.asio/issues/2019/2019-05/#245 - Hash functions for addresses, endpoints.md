# #245 - Hash functions for addresses, endpoints [Closed]

> Username: gumb0  
> Created at: 2019-05-23 17:01:36 UTC  
> Updated at: 2020-12-30 01:05:13 UTC  
> Closed at: 2020-12-30 01:05:12 UTC  
> Url: https://github.com/boostorg/asio/issues/245  

It would be helpful to provide `std::hash` specializations for the types such as `ip::address`, `ip::tcp::endpoint`, `ip::udp::endpoint`, to make them usable with `std::unordered_map`, `std::unordered_set`

---

## Comment 1

> Username: nbougalis  
> Created at: 2020-11-28 23:24:08 UTC  
> Updated at: 2020-11-28 23:48:58 UTC  
> Url: https://github.com/boostorg/asio/issues/245#issuecomment-735304127  

I think this is an excellent idea. Also, hello to my friends from the Ethereum world! :wave:

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:05:11 UTC  
> Url: https://github.com/boostorg/asio/issues/245#issuecomment-752292252  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#696](https://github.com/chriskohlhoff/asio/issues/696).
