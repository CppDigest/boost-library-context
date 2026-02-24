# #2230 - Construct websocket ssl stream from asio::ssl::stream [Closed]

> Username: amirfi95  
> Created at: 2021-05-12 15:31:15 UTC  
> Updated at: 2021-05-12 15:45:53 UTC  
> Closed at: 2021-05-12 15:45:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2230  

I have difficulties constructing a  
`websocket::stream<net::ssl::stream<tcp::socket>>`  
or   
`websocket::stream<beast::ssl_stream<tcp::socket>>`  
  
from an `asio::ssl::stream`.  
  
Boost version 1_73.  
  
The ssl connection has already been established and the handshake has been done on the asio::ssl::stream.  
The next thing I'd like to do is to send/receive websocket upgrades.  
  
  
Is that possible, am I missing something ?  
Or is it necessary to perform the handshake on the beast::ssl_stream, like done in the examples.  
I don't have the ssl context at hand anymore and I'd like to use the existing tcp/ssl code without refactoring it to use beast streams.  
  
This code:  
`  WebSocketSession::WebSocketSession(net::ssl::stream<tcp::socket>&& sslStream)  
  {  
    websocket::stream<net::ssl::stream<tcp::socket>> s{ std::move(sslStream) };  
  }  
  `  
    
  leads to the following compile error:  
     error C2280: 'boost::asio::ssl::stream<boost::asio::ip::tcp::socket>::stream(const boost::asio::ssl::stream<boost::asio::ip::tcp::socket> &)': attempting to reference a deleted function.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-12 15:37:40 UTC  
> Updated at: 2021-05-12 15:37:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2230#issuecomment-839873429  

Asio's `ssl::stream` is not moveable.  
  
Beast's is.  
  
websocket::stream has a constructor overload to deal with this. It wil forward all constructor arguments to the embedded stream, thus:  
  
```  
    websocket::stream< net::ssl::stream < net::ip::tcp::socket > > > { ioc, sslctx };  
```  
  
Will work.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-05-12 15:37:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2230#issuecomment-839873630  

Yes it is possible in theory, but asio's SSL stream is not move-constructible. You need to use the one from beast: https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__ssl_stream.html

---

## Comment 3

> Username: amirfi95  
> Created at: 2021-05-12 15:45:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2230#issuecomment-839879672  

Thank you guys for the quick responses!!  
Okay I thought I  was missing something obvious :)  
I will avoid asio::ssl::stream in favor of beast::ssl_stream
