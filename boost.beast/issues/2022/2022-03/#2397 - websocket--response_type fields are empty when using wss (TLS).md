# #2397 - websocket::response_type fields are empty when using wss (TLS) [Closed]

> Username: Gomox11  
> Created at: 2022-03-10 11:52:45 UTC  
> Updated at: 2022-03-10 17:02:38 UTC  
> Closed at: 2022-03-10 16:38:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2397  

I need to extract the `http::field::sec_websocket_protocol` from a server's handshake response.  
  
When using ws/http, I'm able to read all the included fields just fine.  
  
```  
[DEBUG]websocket_stream.cpp[122]: HandshakeHandler(): WebSocket handshake successful  
Printing all fields:  
Date = Thu, 10 Mar 2022 11:49:01 GMT  
Connection = upgrade  
Upgrade = websocket  
Sec-WebSocket-Accept = CPSjBfUOHbnPHQ5t0ivK4Gg1Pn0=  
Sec-WebSocket-Protocol = protocol1.5  
HTTP Response: HTTP/1.1 101 Switching Protocols  
Date: Thu, 10 Mar 2022 11:49:01 GMT  
Connection: upgrade  
upgrade: websocket  
sec-websocket-accept: CPSjBfUOHbnPHQ5t0ivK4Gg1Pn0=  
sec-websocket-protocol: protocol1.5  
```  
  
However, **when using TLS, not a single field shows up**. Only the HTTP response code can be printed by using the shift operator.  
  
```  
[DEBUG]websocket_stream.cpp[122]: HandshakeHandler(): WebSocket handshake successful  
Printing all fields:  
HTTP Response: HTTP/1.1 200 OK  
  
terminate called after throwing an instance of 'boost::wrapexcept<std::out_of_range>'  
  what():  field not found  
```  
  
I verified with another WS client that the server sets the fields correctly when using TLS.  
  
Am I missing something essential? Does accessing the fields work differently between ws and wss connections?  
  
Code snippets:  
  
```cpp  
void WebsocketStream::AsyncHandshake() {  
  auto server_handshake_response = std::make_shared<boost::beast::websocket::response_type>();  
  websocket_->async_handshake_ex(  
      *server_handshake_response, endpoint_information_.GetEndpoint(), endpoint_information_.target_,  
      [&](boost::beast::websocket::request_type& m) {  
        m.insert(http::field::sec_websocket_protocol, endpoint_information_.websocket_protocol_);  
        if (endpoint_information_.credentials_) {  
          m.insert(http::field::authorization, endpoint_information_.GetHttpBasicAuthString());  
        }  
      },  
      [&, server_handshake_response](const boost::system::error_code& error) {  
        HandshakeHandler(error, *server_handshake_response); });  
  
  handshake_timeout_timer_.ReStart(SRC_LOC);  
}  
  
void WebsocketStream::HandshakeHandler(const boost::system::error_code& error,  
                                       boost::beast::websocket::response_type server_handshake_response) {  
  if (error) {  
   // ...  
  } else {  
    LOG_DEBUG("WebSocket handshake successful");  
  
    std::cout << "Printing all fields:\n";  
    for(auto const& field : server_handshake_response) {  
      std::cout << field.name() << " = " << field.value() << "\n";  
    }  
  
    std::cout << "HTTP Response: " << server_handshake_response;  
  
    auto negotiated_protocol = server_handshake_response.at(http::field::sec_websocket_protocol).to_string();  
  }  
}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-03-10 11:55:16 UTC  
> Updated at: 2022-03-10 12:42:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1063976593  

I think you forgot to capture the shared pointer to the response by value in  
the completion handler.  
  
You wrote:  
```  
[&](boost::beast::websocket::request_type& m)  
```  
  
You probably meant:  
  
```  
[this, server_handshake_response](boost::beast::websocket::request_type& m)  
```  
  
Because taking a reference of a shared pointer will of course not preserve its contents when the shared pointer goes out of scope after you've called the asynchronous initiating function.

---

## Comment 2

> Username: Gomox11  
> Created at: 2022-03-10 12:43:19 UTC  
> Updated at: 2022-03-10 12:43:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064015252  

Hey @madmongo1,  
  
this line in the above example should capture it by value, shouldn't it?  
  
```cpp  
[&, server_handshake_response](const boost::system::error_code& error) {  
```  
  
The line you mentioned is only responsible for the decorator.  
  
```cpp  
[&](boost::beast::websocket::request_type& m)  
```  
  
Also, since it works for HTTP, I would be confused if this would only cause issues only on HTTPS connections.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-03-10 13:39:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064075292  

> HTTP Response: HTTP/1.1 200 OK  
  
That looks like a default constructed response

---

## Comment 4

> Username: madmongo1  
> Created at: 2022-03-10 14:00:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064094445  

> The line you mentioned is only responsible for the decorator.  
  
You're absolutely right. I was too quick off the mark there.  
  
> Does accessing the fields work differently between ws and wss connections?  
  
No, it's the same code - the websocket stream has the underlying stream type as a template parameter.  
  
Which version is boost beast is this? I remember fixing a bug related to handshake at some point.

---

## Comment 5

> Username: madmongo1  
> Created at: 2022-03-10 14:03:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064097004  

https://github.com/boostorg/beast/commit/b5a94db2a5471a4102dea2498a0640cbb544a087

---

## Comment 6

> Username: Gomox11  
> Created at: 2022-03-10 15:00:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064154695  

@madmongo1 @vinniefalco Thank you for your replies!  
  
The `version.hpp` inside Boost Beast states that it is `BOOST_BEAST_VERSION 189`.  
  
The handshake is successful in both cases (HTTP and HTTPS), so no error code gets set. https://github.com/boostorg/beast/commit/b5a94db2a5471a4102dea2498a0640cbb544a087 seems to be talking about only failed handshakes.

---

## Comment 7

> Username: madmongo1  
> Created at: 2022-03-10 15:19:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064174523  

Yep.   
  
Hmm. I've just committed version 329 to the master branch. Quite a bit of water has flown under the bridge since 189.  
  
May I propose two immediate courses of action?  
  
1. Boil the problem down to a reproducible test, compiled into a 1-file complete program. This allows me to debug it.  
2. upgrade to latest boost to see if that cures it.  
  
If not, then at least I have a way to find the bug in the latest version (assuming that item 1 does not reveal that it's an unrelated issue of UB).

---

## Comment 8

> Username: Gomox11  
> Created at: 2022-03-10 16:38:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064267566  

Hey @madmongo1,  
  
I created a minimal working example as you suggested and... it worked! That fact made me look at my actual source code again. And it really was a dumb mistake on my end, not properly passing the `server_handshake_response` to the websocket stream through my layer of classes.  
  
Thank you very much for your guidance! This issue can be closed now :)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-03-10 16:39:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064267888  

aha :)

---

## Comment 10

> Username: madmongo1  
> Created at: 2022-03-10 17:02:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2397#issuecomment-1064291292  

Never fails ;-)
