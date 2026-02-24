# #1189 - Check type connection [Closed]

> Username: Ludea  
> Created at: 2018-07-14 07:00:59 UTC  
> Updated at: 2018-08-01 08:08:39 UTC  
> Closed at: 2018-08-01 08:08:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1189  

Hello,   
  
I have a tcp server, using boost asio (1.67).  
I have a tcp client, which communicate with the server by tcp.   
I have another client, a browser, using js and websocket.   
I try to connect and send message to the tcp server.   
But how to check if the message is sent by tcp or websocket on server.?  
  
I see the function is_upgrade(), which do the difference between http request and websocket. But I know, websocket is a full duplex communication using tcp.   
I don't know if it is possible to check tcp and websocket.   
  
Thanks.

---

## Comment 1

> Username: Ludea  
> Created at: 2018-07-14 13:31:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405023972  

https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html  
  
I see this, it is the answer to my question!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-07-14 13:34:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405024158  

Glad to hear that the documentation has resolved your issue!

---

## Comment 3

> Username: Ludea  
> Created at: 2018-07-16 15:32:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405287881  

```if(websocket::is_upgrade(req))  
{  
    // Construct the stream, transferring ownership of the socket  
    stream<boost::asio::ip::tcp::socket> ws{std::move(sock)};  
  
    // Clients SHOULD NOT begin sending WebSocket  
    // frames until the server has provided a response.  
    BOOST_ASSERT(buffer.size() == 0);  
  
    // Accept the upgrade request  
    ws.accept(req);  
}  
else  
{  
    // Its not a WebSocket upgrade, so  
    // handle it like a normal HTTP request.  
}  
```  
I use this to check if the connection use websocket.   
```  
void  
    on_resolve(  
        boost::system::error_code ec,  
        tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, "resolve");  
  
        // Make the connection on the IP address we get from a lookup  
        boost::asio::async_connect(  
            ws_.next_layer().next_layer(),  
            results.begin(),  
            results.end(),  
            std::bind(  
                &session::on_connect,  
                shared_from_this(),  
                std::placeholders::_1));  
    }  
```  
I use this to connect from a websocket connection.   
  
But the connection example from asio is different   
```  
void do_accept()  
  {  
    acceptor_.async_accept(  
        [this](boost::system::error_code ec, tcp::socket socket)  
        {  
          if (!ec)  
          {  
            std::make_shared<chat_session>(std::move(socket), room_)->start();  
          }  
  
          do_accept();  
        });  
  }  
```  
It is possible to merge the 2 ways of connection and test the websocket and tcp connection?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-07-16 15:51:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405294206  

> It is possible to merge the 2 ways of connection and test the websocket and tcp connection?  
  
I believe you are asking "how can I process normal HTTP requests and WebSocket upgrade handshakes on the same connection?" That is explained in the documentation:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests  
  
The Advanced server examples also show how to do this:  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L607  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L659

---

## Comment 5

> Username: Ludea  
> Created at: 2018-07-16 19:10:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405349431  

I try to use websocket over ssl.  
I see in include file experimental folder, with ssl_stream class.  
So you support this in boost 1.67 ?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-07-16 19:15:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405350761  

> I try to use websocket over ssl.  
  
The advanced-server-flex example allows SSL over websocket:  
  
https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp

---

## Comment 7

> Username: Ludea  
> Created at: 2018-07-16 19:33:53 UTC  
> Updated at: 2018-07-16 19:34:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405355978  

`#include <boost/beast/experimental/core/ssl_stream.hpp>` file not found

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-07-16 19:41:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-405358077  

`ssl_stream` will be in the next release (Boost 1.68). You don't need it though, as the advanced-server-flex from 1.67 illustrates:  
https://github.com/boostorg/beast/blob/boost-1.67.0/example/advanced/server-flex/advanced_server_flex.cpp

---

## Comment 9

> Username: Ludea  
> Created at: 2018-07-20 13:29:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-406600850  

It is possible to merge http_session and ssl_websocket_session from advanced example server?  
I only have ssl websocket

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-07-20 14:31:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-406618317  

Use **websocket-server-async-ssl**  
  
https://github.com/boostorg/beast/tree/develop/example/websocket/server/async-ssl

---

## Comment 11

> Username: Ludea  
> Created at: 2018-07-25 11:54:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-407728447  

I must use the template derived() into ssl websocket class?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-07-25 13:50:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-407760912  

I don't understand what you are asking. You want a server that can handle both HTTP requests and the WebSocket upgrade?

---

## Comment 13

> Username: Ludea  
> Created at: 2018-07-25 13:57:06 UTC  
> Updated at: 2018-07-25 15:28:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-407763090  

Only Websocket and simple tcp socket, both over ssl  
  
I try to start from advanced flex server ( I use boost 1.68 rc1);  
I don't need detect_ssl class, I know it is a ssl class.  
I try to merge ssl_http_session with ssl_websocket_session with the check of Websocket upgrade   
  
my aproach   
```  
websocket class : std::shared_from_this<websocket>  
if Websocket upgrade   
work with it  
else  
create ssl_tcp_session  
delete the shared pointer  
```  
  
Or I must create an http class, do a check of websocket class, and create a websocket class' stack ? if no create a ssl tcp class' stack ?

---

## Comment 14

> Username: Ludea  
> Created at: 2018-07-29 14:15:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-408680690  

I replace io_service with io_context, follow tutorial from asio. But I can not connect with curl.   
  
```  
tcp_server::tcp_server(boost::asio::io_context& ioc,  
      const tcp::endpoint& endpoint)  
    : acceptor_(io_context, endpoint)  
tcp::endpoint endpoint, std::shared_ptr<std:$  
: acceptor(ioc, endpoint),  
doc_root(doc_root),  
m_room(new chatroom(*this))  
{  
        wait_for_connection();  
}  
  
void do_accept()  
  {  
    acceptor_.async_accept(  
        [this](boost::system::error_code ec, tcp::socket socket)  
        {  
          if (!ec)  
          {  
            std::make_shared<chat_session>(std::move(socket), room_)->start();  
          }  
  
          do_accept();  
        });  
  }  
```  
main.cpp  
```  
 boost::asio::io_context io_context;  
std::shared_ptr<std::string const> const&doc_root = std::make_shared<std::string>("/");  
tcp::endpoint endpoint(tcp::v4(), 4000);  
tcp_server(ioc, endpoint, doc_root);  
io_context.run();  
```  
I use curl 127.0.0.1:4000 and I have connection refused

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-07-29 14:16:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-408680796  

Does the unmodified example server with with curl?

---

## Comment 16

> Username: Ludea  
> Created at: 2018-07-29 14:23:39 UTC  
> Updated at: 2018-07-29 14:23:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-408681199  

Yes, I have only add std::cout to debug

---

## Comment 17

> Username: Ludea  
> Created at: 2018-07-29 14:32:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-408681727  

I try to add connection_ptr new_connection(new tcp_connection(acceptor.get_executor().context()));  
But i never success to add it into do_accept()

---

## Comment 18

> Username: vinniefalco  
> Created at: 2018-07-29 14:42:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-408682352  

I can't really help without a clearly articulated statement of the problem, so my general advice is as follows: start by compiling one of the examples which operates similarly to your desired use-case, to confirm that it works (they do). Then modify the example in small increments to achieve the required functionality. Along the way, if the program malfunctions, then it is highly likely that your last change is the culprit.

---

## Comment 19

> Username: Ludea  
> Created at: 2018-08-01 08:08:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1189#issuecomment-409488697  

It was an syntax issue.  
I success to check if the socket is tcp or websocket
