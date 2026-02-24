# #2090 - It seems no API to bind local endpoint for ssl_stream as a client [Closed]

> Username: eepaul  
> Created at: 2020-09-16 10:58:54 UTC  
> Updated at: 2020-09-16 12:34:04 UTC  
> Closed at: 2020-09-16 12:34:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2090  

I need bind to some local endpoint to send https request.  
...  
beast::ssl_stream<beast::tcp_stream> stream(ex, ctx);  
  
//put this before connect would throw bad file descriptor.  
beast::get_lowest_layer(stream).socket().bind(tcp::endpoint(boost::asio::ip::make_address_v4("192.168.1.8"), 10000));  
  
I checked the source code and could not find an API to bind to local endpoint as a client.  
  
Any help would be appreciated. Thanks

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-16 11:17:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2090#issuecomment-693340116  

You must open() a socket before you can bind() it.

---

## Comment 2

> Username: eepaul  
> Created at: 2020-09-16 12:10:01 UTC  
> Updated at: 2020-09-16 12:10:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2090#issuecomment-693363364  

> You must open() a socket before you can bind() it.  
Thanks for your help.  
```c++  
net::io_context ioc;  
auto ex = net::make_strand(ioc);  
beast::ssl_stream<beast::tcp_stream> stream(ex, ctx);  
  
beast::get_lowest_layer(stream).socket().open(boost::asio::ip::tcp::v4());  
beast::get_lowest_layer(stream).socket().bind(tcp::endpoint(boost::asio::ip::make_address_v4("192.168.1.8"), 20001));  
  
auto ep = tcp::endpoint(boost::asio::ip::make_address_v4("180.101.49.11"), 443);  
beast::get_lowest_layer(stream).connect(ep);  
sleep(500);  
```  
Then with netstat, I can see a connection 192.168.1.8:20001<--->180.101.49.11:443 was established as expected.  
  
But if I used a resolver, there was no error/exception when run the program, but the local port was still randomly chosen.  
```c++  
net::io_context ioc;  
auto ex = net::make_strand(ioc);  
beast::ssl_stream<beast::tcp_stream> stream(ex, ctx);  
  
tcp::resolver resolver(ioc);  
// Look up the domain name  
auto const results = resolver.resolve("www.baidu.com", "443");  
  
beast::get_lowest_layer(stream).socket().open(boost::asio::ip::tcp::v4());  
beast::get_lowest_layer(stream).socket().bind(tcp::endpoint(boost::asio::ip::make_address_v4("192.168.1.8"), 20001));  
  
beast::get_lowest_layer(stream).connect(results);  
sleep(500);  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-09-16 12:11:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2090#issuecomment-693364284  

Yes, connect() will automatically open and bind the socket to an ephemeral port. Manually binding the socket is a lower level operation.

---

## Comment 4

> Username: eepaul  
> Created at: 2020-09-16 12:31:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2090#issuecomment-693373945  

I guess I figured it out.  
```c++  
auto ep = tcp::endpoint(boost::asio::ip::make_address_v4("180.101.49.11"), 443);  
  
//this would call below function in basic_stream.hpp  
//void  
//connect(endpoint_type const& ep)  
// {  
//    socket().connect(ep);  
// }  
// It just connected directly, did not close the socket  first.  
beast::get_lowest_layer(stream).connect(ep);  
```  
but  
```c++  
auto const results = resolver.resolve("www.baidu.com", "443");  
  
  
//this would call  
//template <typename Protocol, typename Executor,  
//    typename Iterator, typename ConnectCondition>  
//Iterator connect(basic_socket<Protocol, Executor>& s, Iterator begin,  
//    Iterator end, ConnectCondition connect_condition,  
//    boost::system::error_code& ec)  
// in connect.hpp, which would close the socket first for each result in the iterator.  
beast::get_lowest_layer(stream).connect(results);  
```
