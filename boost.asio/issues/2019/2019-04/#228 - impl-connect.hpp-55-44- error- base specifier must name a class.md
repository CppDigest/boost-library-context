# #228 - impl/connect.hpp:55:44: error: base specifier must name a class [Closed]

> Username: lewisou  
> Created at: 2019-04-25 08:32:23 UTC  
> Updated at: 2020-12-30 01:03:26 UTC  
> Closed at: 2020-12-30 01:03:25 UTC  
> Url: https://github.com/boostorg/asio/issues/228  

I am using boost 1.70.0 with ndk r15c (arm-linux-androideabi-g++ (GCC) 4.9.x 20150123 (prerelease))  
  
When I followed the tcp examples. I got two errors:  
1. boost/boost/asio/impl/connect.hpp:55:44: error: base specifier must name a class  
  struct legacy_connect_condition_helper : T  
  
2. boost/boost/asio/impl/connect.hpp:237:13: error: no matching function for call to 'call_connect_condition'  
    iter = (detail::call_connect_condition(connect_condition, ec, iter, end));  
  
Please see the attached log.   
[error.log](https://github.com/boostorg/asio/files/3115950/error.log)  
I don't know how to solve them...

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:03:23 UTC  
> Url: https://github.com/boostorg/asio/issues/228#issuecomment-752291925  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#685](https://github.com/chriskohlhoff/asio/issues/685).
