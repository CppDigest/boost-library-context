# #2780 - [Need help] A passive and proactive websocket client [Closed]

> Username: zptan  
> Created at: 2023-12-23 11:58:35 UTC  
> Updated at: 2023-12-24 14:08:19 UTC  
> Closed at: 2023-12-24 14:08:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2780  

It is not a bug, just need help.  
  
Requirements:  
1. Websocket client communicates with a server  
2. The server pings all clients periodically, and all clients must response with same payload in 10 minutes  
3. The client may send a request to the server and receive response  
4. The server does not hold connections over 24 hours  
5. There are different requests (upload, query, delete, etc), and accordingly, responses can be different  
6. Performance is important (connection pool!)  
  
I am struggling designing it.  
  
Naturally, I thought of some entities  
1. `WsConnection` holds a websocket stream  
2. `WsConnectionPool` holds all `WsConnection`s and a thread pool that runs the handlers  
3. `WsClient` sends request and receive response through a `WsConnection` instance  
  
But I found it is hard to design `WsConnection`, it passively responds pings from the server, but also provides the utility to send request and receive response. so a ping message may comes before the response after a request has been sent, right?  
  
`WsClient` probably be accessed in a different thread, so it must not call `WsConnection::Write()` directly, I found [`boost::asio::post()`](https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/post/overload3.html), but I'm not sure if it works here.  
  
```cc  
class WsConnection : public enable_shared_from_this<WsConnection> {  
  tcp::resolver                                           resolver_;  
  websocket::stream<beast::ssl_stream<beast::tcp_stream>> ws_;  
  boost::asio::io_context&                                io_ctx_;  
  boost::asio::ssl::context&                              ssl_ctx_;  
  ...  
public:  
  WsConnection(boost::asio::io_context&   io_ctx,  
               boost::asio::ssl::context& ssl_ctx,  
               const string&              host,  
               const string&              path);  
  
  void OnResolve(...);  
  
  void OnConnect(...);  
  
  void OnHandshake(...);  
  
  ...  
  
  void Write(...) {  
    ws_.async_write(data, [self = share_from_this()](...) {  
      Read(...);  
    });  
  }  
  
  // Process both ping messages, and response for specific request  
  void Read(...) {  
    ws_.async_read(..., [self = share_from_this()](...) {  
      if (IsPing(...)) {  
        Pong(...);  
      } else {  
        // We have different response, so HandleResponse(...) is expected to be dynamically passed,  
        // I don't want to make it as a virtual method since it is a one-shot call,  
        // but I don't know how to make it, pass a func obj?  
        HandleResponse(...);  
      }  
  
      Read(...);  // For coming pings  
    });  
  }  
};  
```  
  
### Question  
So for the requirements, what is the best way to utilize boost::beast?

---

## Comment 1

> Username: ashtum  
> Created at: 2023-12-23 12:46:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2780#issuecomment-1868287441  

[websocket::stream](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html) automatically manages ping and pong operations. To configure timeout values appropriately, you can use [websocket::stream::set_option](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option.html).  
  
To get started, examine examples in the beast/example directory and adapt them to meet your requirements. As a general guide, initiate the design of your application around a single-threaded executor since dealing with concurrency issues may prove challenging. You can later extend your code to utilize a multi-threaded executor if you identify performance issues.

---

## Comment 2

> Username: zptan  
> Created at: 2023-12-23 13:05:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2780#issuecomment-1868290836  

Thx, @ashtum   
  
> [websocket::stream](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html) automatically manages ping and pong operations. To configure timeout values appropriately, you can use [websocket::stream::set_option](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option.html).  
  
The server requires pong with same payload to ping message, can automatic pong with dynamical payload?

---

## Comment 3

> Username: ashtum  
> Created at: 2023-12-23 13:16:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2780#issuecomment-1868292944  

> The server requires pong with same payload to ping message, can automatic pong with dynamical payload?  
  
No you need to handle it manually, but if you use [websocket::stream](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html) you can call async_ping while there is an ongoing async_read or async_write without any issue.

---

## Comment 4

> Username: zptan  
> Created at: 2023-12-23 13:31:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2780#issuecomment-1868295516  

Thx, @ashtum, I will have a try.

---

## Comment 5

> Username: zptan  
> Created at: 2023-12-24 08:25:01 UTC  
> Updated at: 2023-12-24 08:25:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2780#issuecomment-1868461489  

Just found [WebSocket Pong (RFC6455)](https://tools.ietf.org/html/rfc6455#section-5.5.3)  
>  A Pong frame sent in response to a Ping frame must have identical  
   "Application data" as found in the message body of the Ping frame  
   being replied to.  
  
So the automatic pong feature meets the requirement of ping-pong, which helps simplify the design.  
  
BTW, [control_callback](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/control_callback.html) is helpful if we need to cook some data as payload of pong
