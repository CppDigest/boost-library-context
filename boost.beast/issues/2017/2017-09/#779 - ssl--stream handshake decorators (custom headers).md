# #779 - ssl::stream handshake decorators (custom headers) [Closed]

> Username: crashtestdummy32  
> Created at: 2017-09-12 12:51:30 UTC  
> Updated at: 2017-09-12 14:46:15 UTC  
> Closed at: 2017-09-12 14:46:15 UTC  
> Url: https://github.com/boostorg/beast/issues/779  

our server uses custom http headers to authenticate the client (beast).  
with websocket::stream, there exists a *handshake_ex function that allows one to insert headers of their choosing.  
with ssl::stream, i don't see those functions nor a way to obtain a reference to the request_type to do so manually.  
suggestions?  
  
beast v110   
gcc 4.9.4

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-12 13:41:02 UTC  
> Url: https://github.com/boostorg/beast/issues/779#issuecomment-328856684  

The SSL handshake is different from the WebSocket handshake. To establish a Secure WebSocket stream connection the steps are as follows:  
1. connect the socket  
2. perform the SSL handshake  
3. perform the WebSocket handshake  
  
This is demonstrated in the code snippet on this documentation page:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/establishing_connections.html  
  
Does this help?

---

## Comment 2

> Username: crashtestdummy32  
> Created at: 2017-09-12 14:13:05 UTC  
> Url: https://github.com/boostorg/beast/issues/779#issuecomment-328865585  

i don't think so. but maybe i'm missing something.  
  
i'm not at all worried about the ssl-protocol-handshake. let's assume that went fine.  
  
i'm having trouble with the websocket-protocol-handshake when used in conjunction with an ssl-socket.  
  
to illustrate the problem, let's take a look at the websocket-protocol-handshake with a plaintext socket. with a plaintext websocket, when i want insert a custom header within a websocket-protocol-handshake, i would call:  
  
```  
ws.handshake_ex("localhost", "/",  
    [](request_type& m)  
    {  
        m.insert("my_custom_field", "my_custom_value");  
    });  
```  
  
the plaintext websocket-protocol-handshake have these "*handshake_ex" overloads and from what i understand they are decorators that allow us to insert a custom header into the request.   
  
but for ssl streams, those  "*handshake_ex" overloads don't exist, and thus i can't find a way to add a header to the websocket-protocol-handshake. it's crucial that i add a custom websocket-protocol-handshake-header.  
  
sorry that i'm struggling to explain what i'm doing.  
  
i'd be willing to help add it, if it helps.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-12 14:35:09 UTC  
> Updated at: 2017-09-12 14:35:22 UTC  
> Url: https://github.com/boostorg/beast/issues/779#issuecomment-328872324  

>but for ssl streams, those "*handshake_ex" overloads don't exist  
  
Why would the `handshake_ex` overloads not exist? Are you declaring the websocket stream correctly? You should be using something like this:  
```  
boost::beast::websocket::stream<  
    boost::asio::ssl::stream<  
        boost::asio::ip::tcp::socket>> wss{ioc, ctx};  
```  
  
See:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/creating_streams.html

---

## Comment 4

> Username: crashtestdummy32  
> Created at: 2017-09-12 14:46:15 UTC  
> Url: https://github.com/boostorg/beast/issues/779#issuecomment-328875796  

doh!  
i blame my clipboard.
