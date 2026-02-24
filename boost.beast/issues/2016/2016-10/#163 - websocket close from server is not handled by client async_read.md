# #163 - websocket close from server is not handled by client async_read [Closed]

> Username: redboltz  
> Created at: 2016-10-31 03:23:45 UTC  
> Updated at: 2016-11-04 15:18:57 UTC  
> Closed at: 2016-11-04 15:18:57 UTC  
> Url: https://github.com/boostorg/beast/issues/163  

It seems that the event the websocket connection closed by the server is not handled by the client async_read.  
  
I wrote a minimal and complete code that reproduces the issue:  
  
```cpp  
#include <iostream>  
#include <boost/lexical_cast.hpp>  
#include <beast/websocket.hpp>  
  
int main(int argc, char** argv) {  
    std::string port_str = "12345";  
    if (argc == 2) {  
        port_str = argv[1];  
    }  
    auto port = boost::lexical_cast<std::uint16_t>(port_str);  
  
    boost::asio::io_service ios;  
    boost::asio::ip::tcp::acceptor acceptor(  
        ios,  
        boost::asio::ip::tcp::endpoint(boost::asio::ip::tcp::v4(), port));  
  
    beast::websocket::stream<boost::asio::ip::tcp::socket> ws_server(ios);  
    std::cout << "asio async_accept called" << std::endl;  
    acceptor.async_accept(  
        ws_server.next_layer(),  
        [&ws_server]  
        (boost::system::error_code e) {  
            std::cout << "asio accepted e = " << e.message() << std::endl;  
            std::cout << "beast async_accept called" << std::endl;  
            ws_server.async_accept(  
                [&ws_server]  
                (boost::system::error_code const& e) {  
                    std::cout << "beast accepted e = " << e.message() << std::endl;  
                    std::cout << "close called" << std::endl;  
  
                    // close doesn't seems to be handled by client's async_read.  
                    // If I replaced ws_server.close(beast::websocket::close_code::normal);  
                    // with          ws_server.write(boost::asio::buffer("ABC"));  
                    // then it is handled by client's async_read.  
                    ws_server.close(beast::websocket::close_code::normal);  
                }  
            );  
        }  
    );  
  
    beast::websocket::stream<boost::asio::ip::tcp::socket> ws_client(ios);  
    boost::asio::ip::tcp::resolver::query q{"localhost", port_str};  
    boost::asio::ip::tcp::resolver r(ios);  
    auto it = r.resolve(q);  
    std::cout << "async_connect called" << std::endl;  
    boost::asio::async_connect(  
        ws_client.lowest_layer(),  
        it,  
        [&ws_client]  
        (boost::system::error_code const& e,  
         boost::asio::ip::tcp::resolver::iterator) {  
            std::cout << "connected e = " << e.message() << std::endl;  
            std::cout << "async_handshake called" << std::endl;  
            ws_client.async_handshake(  
                "localhost",  
                "/",  
                [&ws_client]  
                (boost::system::error_code const& e) {  
                    std::cout << "handshaked e = " << e.message() << std::endl;  
                    auto op = std::make_shared<beast::websocket::opcode>();  
                    auto sb = std::make_shared<beast::streambuf>();  
                    ws_client.async_read(  
                        *op,  
                        *sb,  
                        [op, sb, &ws_client]  
                        (boost::system::error_code const& e) {  
                            std::cout << "read e = " << e.message() << std::endl;  
                        }  
                    );  
                }  
            );  
        }  
    );  
    ios.run();  
}  
```  
  
The execution result is as follows, and program isn't finished.  
  
```  
asio async_accept called  
async_connect called  
connected e = Success  
async_handshake called  
asio accepted e = Success  
beast async_accept called  
beast accepted e = Success  
close called  
handshaked e = Success  
```  
  
As I commented, If I replaced  
  
```cpp  
ws_server.close(beast::websocket::close_code::normal);  
```  
with  
  
```cpp  
ws_server.write(boost::asio::buffer("ABC"));  
```  
, then the write is handled by client's async_read and program is finished with the following result:  
  
```  
asio async_accept called  
async_connect called  
connected e = Success  
async_handshake called  
asio accepted e = Success  
beast async_accept called  
beast accepted e = Success  
close called  
handshaked e = Success  
read e = Success  
```  
  
I need to close the websocket connection from the server glacefully. Is there any way to do that?   
  
I'm not sure but #69 seems to be related.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-31 11:04:29 UTC  
> Updated at: 2016-10-31 11:10:42 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257266797  

Calling `stream::close` only sends a close frame. It is still the server's responsibility to continue reading from the stream until `error::closed` is returned. This is explained in the documentation, from:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/close/overload2.html  
_"Callers should not attempt to write WebSocket data after initiating the close. Instead, callers should continue reading until an error occurs. A read returning `error::closed` indicates a successful connection closure."_  
  
In your example code (which looks great by the way!), the server is calling `stream::close` and then destroying the socket without reading any frames. This means that when the client receives the close frame and sends the close frame in response, the connection would be reset. You should be seeing something like this:  
  
```  
read e = Connection reset by peer  
```  
  
To fix the server to work correctly, after calling `close` you should loop and either process or discard messages until receiving `error::closed`:  
  
```  
ws_server.close(beast::websocket::close_code::normal);  
beast::error_code ec;  
do  
{  
  beast::streambuf sb;  
  beast::websocket::opcode op;  
  ws_server.read(op, sb, ec);  
}  
while(! ec);  
if(ec != beast::websocket::error::closed)  
  std::cout << "read: " << ec.message() << std::endl;  
```  
  
#69 has to do with HTTP "Connection: keep-alive" and "Connection: close", it is unrelated to WebSockets.

---

## Comment 2

> Username: redboltz  
> Created at: 2016-10-31 11:54:21 UTC  
> Updated at: 2016-10-31 11:59:07 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257275293  

Thank you for the response. I understand it :)  
  
I updated my code. I need to use async_read on the server because after sending a close frame the client need to the chance to process the frame. I use the single io_service object for both server and client.  
In addition, after receiving `error::closed` at the server, the server need to close TCP/IP layer socket.  
  
``` cpp  
#include <iostream>  
#include <boost/lexical_cast.hpp>  
#include <beast/websocket.hpp>  
  
int main(int argc, char** argv) {  
    std::string port_str = "12345";  
    if (argc == 2) {  
        port_str = argv[1];  
    }  
    auto port = boost::lexical_cast<std::uint16_t>(port_str);  
  
    boost::asio::io_service ios;  
    boost::asio::ip::tcp::acceptor acceptor(  
        ios,  
        boost::asio::ip::tcp::endpoint(boost::asio::ip::tcp::v4(), port));  
  
    beast::websocket::stream<boost::asio::ip::tcp::socket> ws_server(ios);  
    std::cout << "asio async_accept called" << std::endl;  
    acceptor.async_accept(  
        ws_server.next_layer(),  
        [&ws_server]  
        (boost::system::error_code e) {  
            std::cout << "asio accepted e = " << e.message() << std::endl;  
            std::cout << "beast async_accept called" << std::endl;  
            ws_server.async_accept(  
                [&ws_server]  
                (boost::system::error_code const& e) {  
                    std::cout << "beast accepted e = " << e.message() << std::endl;  
                    std::cout << "close called" << std::endl;  
                    ws_server.close(beast::websocket::close_code::normal);  
                    auto read_handler =  
                        std::make_shared<std::function<void(boost::system::error_code const& e)>>();  
                    auto sb = std::make_shared<beast::streambuf>();  
                    auto op = std::make_shared<beast::websocket::opcode>();  
                    *read_handler =  
                        [sb, op, read_handler, &ws_server]  
                        (boost::system::error_code const& e) {  
                        std::cout << "server read e = " << e.message() << std::endl;  
                        if (e) {  
                            if(e == beast::websocket::error::closed) {  
                                ws_server.next_layer().close();  
                            }  
                            return;  
                        }  
                        std::cout << "server async_read called" << std::endl;  
                        ws_server.async_read(*op, *sb, *read_handler);  
                    };  
                    std::cout << "server async_read called" << std::endl;  
                    ws_server.async_read(*op, *sb, *read_handler);  
                }  
            );  
        }  
    );  
  
    beast::websocket::stream<boost::asio::ip::tcp::socket> ws_client(ios);  
    boost::asio::ip::tcp::resolver::query q{"localhost", port_str};  
    boost::asio::ip::tcp::resolver r(ios);  
    auto it = r.resolve(q);  
    std::cout << "async_connect called" << std::endl;  
    boost::asio::async_connect(  
        ws_client.lowest_layer(),  
        it,  
        [&ws_client]  
        (boost::system::error_code const& e,  
         boost::asio::ip::tcp::resolver::iterator) {  
            std::cout << "connected e = " << e.message() << std::endl;  
            std::cout << "async_handshake called" << std::endl;  
            ws_client.async_handshake(  
                "localhost",  
                "/",  
                [&ws_client]  
                (boost::system::error_code const& e) {  
                    std::cout << "handshaked e = " << e.message() << std::endl;  
                    auto op = std::make_shared<beast::websocket::opcode>();  
                    auto sb = std::make_shared<beast::streambuf>();  
                    std::cout << "client async_read called" << std::endl;  
                    ws_client.async_read(  
                        *op,  
                        *sb,  
                        [op, sb, &ws_client]  
                        (boost::system::error_code const& e) {  
                            std::cout << "client read e = " << e.message() << std::endl;  
                        }  
                    );  
                }  
            );  
        }  
    );  
    ios.run();  
}  
```  
  
I got the following output and the program is finished.  
  
```  
asio async_accept called  
async_connect called  
connected e = Success  
async_handshake called  
asio accepted e = Success  
beast async_accept called  
beast accepted e = Success  
close called  
server async_read called  
handshaked e = Success  
client async_read called  
server read e = WebSocket connection closed normally  
client read e = WebSocket connection closed normally  
```  
  
It seems that the program worked perfectly.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-31 12:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257277841  

Actually, you don't need to close the socket, Beast will handle that for you (since it prescribed by the WebSocket protocol specification). However, this is not explained very well in the documentation, I will update it.

---

## Comment 4

> Username: redboltz  
> Created at: 2016-10-31 12:15:06 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257278911  

> Actually, you don't need to close the socket, Beast will handle that for you (since it prescribed by the WebSocket protocol specification). However, this is not explained very well in the documentation, I will update it.  
  
Which socket I don't need to close? If I comment out   
  
``` cpp  
                                ws_server.next_layer().close();  
```  
  
program is not finished.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-10-31 12:27:01 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257280928  

@redboltz That looks like a bug. RFC6455 section 7.1.2 requires that after both sending and receiving a close frame, the implementation should "Close the WebSocket connection" (which closes the underlying TCP/IP socket). Beast already has code to do this, and it executes when the connection is failed (for example, when receiving an invalid message frame). But it should also close the socket after receiving the final close frame. I will open an issue.  
  
One other thing, note that you are calling `stream::close` which is synchronous. You probably want to use `stream::async_close` (since all the other calls in your example are asynchronous).

---

## Comment 6

> Username: redboltz  
> Created at: 2016-10-31 12:59:47 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257287449  

Thank you @vinniefalco. I understand current situation.  
After committing the fix for that, I will remove TCP/IP layer closing code from my program.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-10-31 15:01:10 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257317612  

The _close_ branch has the fix, please let me know if it works:  
https://github.com/vinniefalco/Beast/commits/close

---

## Comment 8

> Username: redboltz  
> Created at: 2016-10-31 15:04:47 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257318678  

It works perfectly!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-11-02 14:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-257877047  

You have written some pretty great, compact asynchronous code do you mind if I use it in the Beast examples?

---

## Comment 10

> Username: redboltz  
> Created at: 2016-11-03 00:39:46 UTC  
> Url: https://github.com/boostorg/beast/issues/163#issuecomment-258041164  

> You have written some pretty great, compact asynchronous code   
  
Thank you.  
  
> do you mind if I use it in the Beast examples?  
  
Of course not. Please use it.
