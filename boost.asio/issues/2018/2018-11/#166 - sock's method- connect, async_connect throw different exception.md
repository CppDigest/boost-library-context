# #166 - sock's method: connect, async_connect throw different exception. [Closed]

> Username: Eggache666  
> Created at: 2018-11-16 02:42:37 UTC  
> Updated at: 2020-12-30 00:57:16 UTC  
> Closed at: 2020-12-30 00:57:15 UTC  
> Url: https://github.com/boostorg/asio/issues/166  

When using sock's method, 'connect' and 'async_connect' to connect to a server that does not exist, the exception thrown is different: (boost::system::system_error)  
  
the  message of e.what():  
'connect": "connect: Connection refused"  
'async_connect':  "Connection refused"  
  
I think it is better to output the same information.  
  
platform: ubuntu18.10, boost1.68, clang7.0.0

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:57:14 UTC  
> Url: https://github.com/boostorg/asio/issues/166#issuecomment-752290637  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#652](https://github.com/chriskohlhoff/asio/issues/652).
