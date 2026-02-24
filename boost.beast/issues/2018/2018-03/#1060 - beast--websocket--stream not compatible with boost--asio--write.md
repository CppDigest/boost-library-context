# #1060 - beast::websocket::stream not compatible with boost::asio::write [Closed]

> Username: gegles  
> Created at: 2018-03-08 23:43:19 UTC  
> Updated at: 2022-10-28 05:42:59 UTC  
> Closed at: 2018-03-09 18:47:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1060  

I have a template class that should work as long as the type passed complies with the asio Sync/Async WriteStream requirements. I thought Beast Websocket stream were supposed to be compliant...  
  
If this can't be addressed, what would be a good workaround?  
  
### Version of Beast: 163  
  
### Steps necessary to reproduce the problem  
  
```c++  
#include <boost/asio/write.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
  
int main() {  
    namespace net = boost::asio;  
    namespace websocket = boost::beast::websocket;  
  
    net::io_context ioc;  
    websocket::stream<net::ip::tcp::socket> ws{ioc};  
  
    ws.next_layer().connect(net::ip::tcp::endpoint{net::ip::address_v4::loopback(), 12345});  
    ws.handshake("localhost", "/");  
  
    // This compiles  
    ws.write(net::buffer(std::string("Success")));  
  
    // This does not compile  
    net::write(ws, net::buffer(std::string("Failure")));  
    return EXIT_SUCCESS;  
}  
```  
### All relevant compiler information  
  
```  
boost/asio/impl/write.hpp:53:23: error: no matching member function for call to 'write_some'  
        tmp.consume(s.write_some(tmp.prepare(max_size), ec));  
                    ~~^~~~~~~~~~  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-09 01:37:12 UTC  
> Updated at: 2018-03-09 01:37:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1060#issuecomment-371684406  

`beast::websocket::stream` is not intended to meet the requirements of **SyncWriteStream** or **AsyncWriteStream**. It sounds like what you want is a websocket tunnel, like this:  
https://github.com/mhzed/wstunnel

---

## Comment 2

> Username: gegles  
> Created at: 2018-03-09 02:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1060#issuecomment-371688427  

@vinniefalco Thanks for the quick reply. I must have read the doc wrong about `beast::websocket::stream` meeting the SyncWriteStream requirements....  
  
I am using Beast+Asio to build some kind of generic bridge between a few stream-based protocols (WS or TCP) to/from a custom protocol of ours.... so it would have been nice if the websocket::stream class would also meet the WriteStream reqs....  
  
I haven't dug too deep yet so it may be a stupid question, but it seems so close, what is stopping it from being 100% compliant? The result would be that when asio::write is called, it only returns once the full buffer is sent...  
  
Any thoughts?  
  
I'll see if I can implement the proper async_write_some logic...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-03-09 04:06:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1060#issuecomment-371707609  

The docs state that the template type used to instantiate the `websocket::stream` must meet the requirements of Sync or Async read/write streams.  
  
>what is stopping it from being 100% compliant?  
  
Because it is out-of-scope for the library. The design philosophy of Beast is to avoid making odd decisions on behalf of the user. It is fairly trivial to modify the websocket stream class to support this, but then it is only useful in the case of the websocket tunnel as you described. I feel that this is better addressed in a separate library that uses the `websocket::stream` as a building block. There are other aspects of maintaining the tunnel (such as establishing the connection) which should be part of a robust tunnel library which of course Beast cannot possibly hope to achieve, since Beast does not use or know about sockets.

---

## Comment 4

> Username: gegles  
> Created at: 2018-03-09 18:47:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1060#issuecomment-371909525  

No problem @vinniefalco, I completely understand. Thanks for gifting the world with the great library!

---

## Comment 5

> Username: Spongman  
> Created at: 2022-10-28 05:42:45 UTC  
> Updated at: 2022-10-28 05:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1060#issuecomment-1294488196  

ugh. i just ran into this. this seems like a really obvious gap: being able to easily serialize `message`s over a `websocket::stream`. why require a whole other library when 99% of the functionality is already there?
