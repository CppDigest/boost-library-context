# #2857 - Limitation of `websocket::stream::accept(...)` and `websocket::stream::async_accept(...)`. [Closed]

> Username: Ramirisu  
> Created at: 2024-04-24 12:32:35 UTC  
> Updated at: 2024-04-27 16:55:26 UTC  
> Closed at: 2024-04-27 16:55:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2857  

Beast version: `latest`  
  
`websocket::stream::accept(...)` and `websocket::stream::async_accept(...)` perform following tasks internally:  
1. Creates a response and adds some headers to the response according to the request headers.  
2. Users can customize the response by using the `decorator`.  
3. Then it sends the response to client to complete the websocket handshake.  
4. Change the stream to `server` role.  
  
Looks great, they automatically do everything for us.  
However, consider that if we want to integrate it into some web framework that implementing middlewares. The `response` is created ***internally*** so we can't pass it back to the chain of middlewares (middlewares probably need to read/modify headers/body of the `response`).  
  
From my imagination there should be some APIs to perform:  
1. Build the `response` and return it to us so we can pass it back to the chain of middlewares. (Ex: `initial_accept()`)  
2. An API to accept a `response` and then sends it to the client and complete the handshake of the `websocket::stream`. (Ex: `complete_accept()`)  
  
```  
//  --------- Session ------------------------------------------  
// |   * read request from socket                               |  
// |  ------- Middlewares ------------------------------------  |  
// | |   * read/modify request                                | |  
// | |  --------- Handler ----------------------------------  | |  
// | | |  auto res = ws.initial_accept(req);                | | |  
// | | |  return res;                                       | | |  
// | |  ----------------------------------------------------  | |  
// | |   * read/modify response                               | |  
// |  --------------------------------------------------------  |  
// |   * send the response to client to complete handshake      |  
// |     ws.complete_accept(res);                               |  
// |                                                            |  
// |   * read/write the websocket messages                      |  
//  ------------------------------------------------------------  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-04-24 13:02:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2857#issuecomment-2074897314  

What middleware are you working with? Can you show example code?

---

## Comment 2

> Username: ashtum  
> Created at: 2024-04-24 13:10:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2857#issuecomment-2074914720  

If I understand correctly, you want to delay the decoration of handshake response in order to execute asynchronous operations in between. While this isn't possible with the current API, you can read the upgrade request separately and accept it in a different step:  
[Inspecting HTTP Requests](https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests)  
```C++  
http::read(sock, buffer, req);  
  
if(websocket::is_upgrade(req))  
{  
    stream<tcp_stream> ws(std::move(sock));  
  
    ws.accept(req);  
}  
```  
While this isn't precisely what you mentioned, it may provide you with the opportunity to achieve similar results.

---

## Comment 3

> Username: Ramirisu  
> Created at: 2024-04-24 13:54:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2857#issuecomment-2075009511  

The middleware and handler would be roughly like these. The upgrade response should be obtained in the `handler` and then return it back to the middleware in order to read/modify it. Then we perform the upgrade (send the modified response to client) after the whole middleware chain completes.  
  
```cpp  
// where `web::request` and `web::response` are type-erased interface  
  
awaitable<web::response> http_handler(web::request& req)  
{  
  co_return web::response::ok().build();  
}  
  
awaitable<web::response> websocket_handler(web::request& req)  
{  
  // auto [ec] = co_await req.accept(); which calls  
  // `beast::websocket::stream::async_accept()` internally.  
  //  
  // we can't call `beast::websocket::stream::async_accept` here, the resonpse  
  // can't be return back to the middleware flow.  
  
  // !!! NEED an api to get the upgrade response from  
  // `beast::websocket::stream`. !!!  
  web::response res = req.initial_upgrade(); // internally calls that api  
  
  co_return res;  
}  
  
template <typename NextService>  
class trace_id_middleware {  
  NextService next_; // next middleware or handler  
  
public:  
  awaitable<web::response> operator()(web::request& req)  
  {  
    web::response res = co_await next_(req);  
  
    // modify the upgrade response  
    res.headers().insert("x-trace-id", uuid::create());  
  
    co_return res;  
  }  
};  
  
awaitable<void> handle_session(tcp_stream stream)  
{  
  // which internally uses `beast::http::request` and `beast::http::read_header`  
  web::request req = read_request_header(stream);  
  
  bool upgrade = req.is_upgrade();  
  
  auto route = router.get_route(...);  
  
  // which calls into trace_id_middleware<handler>  
  web::response res = co_await route.serve(req);  
  
  if (upgrade) {  
    // websocket flow  
  
    // !!! NEED an API to complete the handshake !!!  
    co_await res.complete_upgrade();  
  
    // websocke read/write  
  } else {  
    // http response  
  }  
}  
  
```
