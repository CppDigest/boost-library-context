# #22 - Port uses std::string type [Closed]

> Username: wcy168  
> Created at: 2022-08-09 07:34:54 UTC  
> Updated at: 2022-08-17 00:20:53 UTC  
> Closed at: 2022-08-17 00:20:53 UTC  
> Url: https://github.com/boostorg/redis/issues/22  

The port number uses the std::string type in config, which is counter intuitive. It is recommended to use the unsigned short type.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-08-16 19:40:15 UTC  
> Url: https://github.com/boostorg/redis/issues/22#issuecomment-1217083894  

Sorry for the delay, it has been some busy days. Specifying host and port as strings is usually more convenient to the user. This is also what Asio does: https://www.boost.org/doc/libs/1_80_0/doc/html/boost_asio/reference/ip__basic_resolver/resolve/overload3.html

---

## Comment 2

> Username: wcy168  
> Created at: 2022-08-17 00:20:47 UTC  
> Url: https://github.com/boostorg/redis/issues/22#issuecomment-1217301895  

ok
