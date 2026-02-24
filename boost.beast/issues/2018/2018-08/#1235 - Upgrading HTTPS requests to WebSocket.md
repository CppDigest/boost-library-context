# #1235 - Upgrading HTTPS requests to WebSocket [Closed]

> Username: firewave  
> Created at: 2018-08-27 12:14:51 UTC  
> Updated at: 2018-11-04 20:37:20 UTC  
> Closed at: 2018-11-04 20:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1235  

I have existing working code which upgrades a HTTP request to a WebSocket one based on the code as found under _Passing HTTP Requests_ at https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html  
  
Now that code has to be updated to do the same for HTTPS. That seems to be problematic as it is not possible to use an existing ```boost::asio::ssl::stream<tcp::socket>``` when creating a ```websocket::stream<boost::asio::ssl::stream<tcp::socket>>```. I checked various implementation and all of those start out with a ```websocket::stream``` already and access the ssl one via ```next_layer()```. So I was wondering if that was the way to do this or if there also exists an easy way to do the upgrade as there is for HTTP.  
   
I am using Boost 1.67.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-27 17:38:37 UTC  
> Updated at: 2018-08-27 18:09:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1235#issuecomment-416306096  

> it is not possible to use an existing boost::asio::ssl::stream<tcp::socket> when creating a websocket::stream<boost::asio::ssl::stream<tcp::socket>>  
  
That's what `beast::ssl_stream` is for, it makes the thing movable, allowing the websocket stream to be move-constructible and also the `ssl_stream` to be constructible from a moved-in socket. See:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__ssl_stream.html  
https://github.com/boostorg/beast/blob/1da229a27c6f0539d422bcedbcf47cfe2517164a/example/advanced/server-flex/advanced_server_flex.cpp#L548  
https://github.com/boostorg/beast/blob/1da229a27c6f0539d422bcedbcf47cfe2517164a/example/advanced/server-flex/advanced_server_flex.cpp#L559  
  
Note: You might need Boost 1.68 for these to be available to you.

---

## Comment 2

> Username: firewave  
> Created at: 2018-08-27 20:22:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1235#issuecomment-416355970  

Okay, it just being in 1.68 and also being experimental it's obvious I have not been able to figure this out. Thanks for pointing that out.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-27 20:49:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1235#issuecomment-416363826  

1.67 also has it, but it is in the examples/ directory instead:  
https://github.com/boostorg/beast/blob/b4cb4f1fd3d9d1eca90f44e59e1ca0b5260d0c13/example/common/ssl_stream.hpp

---

## Comment 4

> Username: firewave  
> Created at: 2018-08-29 19:41:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1235#issuecomment-417079769  

I was able to do the upgrade using the class from the 1.67 examples for now. Thanks for pointing that out!

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-10-28 19:48:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1235#issuecomment-433735569  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-11-04 20:37:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1235#issuecomment-435703632  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
