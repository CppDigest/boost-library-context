# #2238 - WebSocket and target routing [Closed]

> Username: Tectu  
> Created at: 2021-05-22 13:22:05 UTC  
> Updated at: 2021-05-23 14:13:20 UTC  
> Closed at: 2021-05-23 14:13:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2238  

The existing WebSocket examples seem simple in the way that most of the time they just echo the received message back. Different is the `example/websocket/chat-multi` example but that also works on a single "target".  
  
I'd like to know how I can build an application where I can accept websocket connections on different, specific endpoints/targets.  
On the HTTP side this is easy by inspecting the corresponding `boost::beast::http::request` object as this carries the resource/target string.  
  
Basically I'd like to extend [Malloy](https://github.com/tectu/malloy) so the user can not only register HTTP handlers but also websocket handlers on specific targets.  
Currently, the API can be used like this:  
```cpp  
int main()  
{  
    const std::filesystem::path doc_root = "../../../../examples/static_content";  
  
    // Create malloy controller config  
    malloy::server::controller::config cfg;  
    cfg.interface   = "127.0.0.1";  
    cfg.port        = 8080;  
    cfg.doc_root    = doc_root;  
    cfg.num_threads = 5;  
  
    // Create malloy controller  
    malloy::server::controller c;  
    if (!c.init(cfg)) {  
        std::cerr << "could not start controller." << std::endl;  
        return EXIT_FAILURE;  
    }  
  
    // Create the router  
    auto router = c.router();  
    if (router) {  
        using namespace malloy::http;  
  
        // A simple GET route handler  
        router->add(method::get, "/", [](const auto& req) {  
            response res{status::ok};  
            res.body() = "<html><body><h1>Hello World!</h1><p>some content...</p></body></html>";  
            return res;  
        });  
  
        // Add a route to an existing file  
        router->add(method::get, "/file", [doc_root](const auto& req) {  
            return generator::file(doc_root, "index.html");  
        });  
  
        // Add some file serving  
        router->add_file_serving("/files", doc_root);  
    }  
  
    // Start  
    c.start();  
  
    return EXIT_SUCCESS;  
}  
```  
What I would like is adding something like:  
```cpp  
router->add_websocket("/some/target/echo", [](const auto& payload) {  
   return payload;  
});  
  
router->add_websocket("/some/other/target/notifications", [](const auto& payload) {  
  // Do something with the payload  
  // ...  
  
  return "Some response for the handled payload";  
});  
```  
How can I do this using boost.beast?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-22 14:09:43 UTC  
> Updated at: 2021-05-22 14:12:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2238#issuecomment-846413704  

Hi there, it's reasonably straightforward given the following two facts:  
  
1. There is a specialisation of `websocket::stream<>` where the template argument is a _reference_ to a stream.  
2. There is an overload of `websocket::async_handshake` which takes the `http::request` by reference.   
  
Together, these give you your conditional upgrade which would be performed by the router.  
  
Re 1:  
  
```  
using my_ssl_or_tcp_stream = ...;  
  
using wstype = beast::websocket::stream<my_ssl_or_tcp_stream&>;  
  
my_ssl_or_tcp_stream transport;  
wstype my_stream { transport };  // construct with reference  
```

---

## Comment 2

> Username: Tectu  
> Created at: 2021-05-22 14:38:07 UTC  
> Updated at: 2021-05-22 15:51:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2238#issuecomment-846417194  

Thank you for your reply!  
  
I'd like to check whether I understand you correctly, so here's my recap: The current websocket implementation can be left as-is. I just create an extra `beast::websocket::stream` on top of the existing one but this time passing the reference to the existing stream. Then, I modify my call to `beast::websocket::async_handshake()` to use an overload which accepts `http::request`.  
  
Looking at the documentation of [`beast::websocket::stream::async_handshake()`](https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_handshake.html) I am unable to spot an overload that accepts an `http::request`. I do see one that accepts a reference to `response_type` though. What am I missing or understanding correctly?  
  
From there, I take it that the `http::request` will be passed to the `on_handsake()` handler?  
  
In general I'd appreciate it if you could add a few more lines of code snippets :)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-05-22 15:57:10 UTC  
> Updated at: 2021-05-22 15:57:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2238#issuecomment-846427684  

If you're writing a server you need to use `websocket::stream::async_accept`.

---

## Comment 4

> Username: Tectu  
> Created at: 2021-05-22 16:19:34 UTC  
> Updated at: 2021-05-22 16:20:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2238#issuecomment-846430771  

Gotcha, thanks!  
  
How can I retrieve the request that was passed to `websocket::stream::async_accept()`?  
The handler certainly doesn't seem to provide it as an argument.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-05-22 17:35:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2238#issuecomment-846440459  

> How can I retrieve the request that was passed to websocket::stream::async_accept()?  
  
See:  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests

---

## Comment 6

> Username: Tectu  
> Created at: 2021-05-23 14:13:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2238#issuecomment-846569919  

Thank you guys, I got it working :)  
Your help is much appreciated!
