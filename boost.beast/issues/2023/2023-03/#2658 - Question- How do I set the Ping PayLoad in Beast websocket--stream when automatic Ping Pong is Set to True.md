# #2658 - Question: How do I set the Ping PayLoad in Beast websocket::stream when automatic Ping Pong is Set to True? [Closed]

> Username: marketfker  
> Created at: 2023-03-21 08:39:18 UTC  
> Updated at: 2023-03-24 04:33:21 UTC  
> Closed at: 2023-03-24 04:33:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2658  

I am currently trying to implement an async Websocket Client. My timeout options are set in the following way to enable automatic ping pong. However I struggle to find a way to define the ping payload message. Are there any ways to achieve that? Also are there anyways to set how frequent the ping messages are sent out from my side?  
  
Below is code snippet that I tried to set the ping pong options  
  
```  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
websocket::stream<beast::ssl_stream<beast::tcp_stream>> ws  
websocket::stream_base::timeout timeoutOpt{  
        std::chrono::seconds(10),   // handshake timeout  
        std::chrono::seconds(20),       // idle timeout. Any ways to set the ping interval as well?  
        true   //enable ping-pong to keep alive  
    };  
m_ws.set_option(timeoutOpt);  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-03-21 08:41:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2658#issuecomment-1477451736  

I don't think ping/pong have messages.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-03-21 14:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2658#issuecomment-1477967995  

Pings and pongs can have a small payload but if you want to use that payload, then you cannot use the automatic ping/pong feature of Beast websocket streams.
