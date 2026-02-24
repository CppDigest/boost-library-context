# #122 - HTTP/2 design [Closed]

> Username: ahmedcharles  
> Created at: 2016-10-10 14:30:23 UTC  
> Updated at: 2017-09-01 03:02:51 UTC  
> Closed at: 2017-09-01 03:02:51 UTC  
> Url: https://github.com/boostorg/beast/issues/122  

Following on from the short discussion in #121.  
  
@vinniefalco proposed the following, based on experience with `asio::ssl::stream` and `beast::websocket::stream`:  
  
``` C++  
template<class NextLayer>  
class http::session_v2  
{  
  NextLayer next_layer_;  
  ...  
public:  
  ...  
  /** Deserialize a HTTP message from the stream.  
  
    @param id The message-identifier, unique for each message.  
    @param msg The received message.  
    @param ec Set to the error, if any occurred/  
  */  
  template<bool isRequest, class Body, class Headers>  
  void  
  read(  
    std::size_t& id,  
    message<isRequest, Body, Headers>& message,  
    error_code& ec);  
};  
```  
  
I think that's a great starting point, given that HTTP/2 requires connection state when sending messages.  
  
Another point of discussion was whether free functions could be easily used or whether function on the stream should be. Technically, given that the current functions take a stream and message, e.g. `write(tcp_stream, message_v1, error_code);`, there's no reason that an HTTP/2 api based on a custom stream and message object could not be similarly implemented as `write(http2_stream, message_v2, error_code);`. This would result in the api being very familiar to users for operations that are common between HTTP/1 and HTTP/2.  
  
Though, I think there would have to be a three tier sort of hierarchy, like:  
  
```  
class message {  
    // contains body, headers  
};  
class stream {  
    // represents the HTTP/2 concept of a stream  
};  
class session {  
    // returns a new stream for use in write/read free functions  
    stream new_stream();  
};  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-10 14:46:29 UTC  
> Url: https://github.com/boostorg/beast/issues/122#issuecomment-252642732  

> Another point of discussion was whether free functions could be easily used or whether function on the stream should be. Technically, given that the current functions take a stream and message...there's no reason that an HTTP/2 api based on a custom stream and message object could not be similarly implemented  
  
Its true that there's a relationship between `f(t, ...)` and `t.f(...)`, and this is reflected in the Unified Call Syntax proposal (http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4165.pdf). However, Herb makes pretty good arguments for why member function syntax is preferred (for example, it is more friendly to "auto complete" features in the IDE).  
  
Even so, HTTP/2 is not so simple that it only includes reading and writing messages. There needs to be an interface for flow control, and a way to get and set metadata on individual messages, streams, and the session itself. It would be clumsy to have a set of free functions which just duplicate behavior provided by member functions.  
  
In general, consistency is a good thing, but I think in this case we're paying more than what we're getting by trying to make HTTP/2 free-function driven.

---

## Comment 2

> Username: ahmedcharles  
> Created at: 2016-10-10 14:54:00 UTC  
> Url: https://github.com/boostorg/beast/issues/122#issuecomment-252645560  

I generally find auto complete to be of marginal value, since in many cases it either doesn't work or is implemented in a way that breaks my flow when I know what to type and popups just get in my way. But that's personal preference.  
  
I'm similarly, not a fan of unified call syntax since I think it doesn't pull it's weight for the complexity added. I feel much the same about uniform initialization syntax that was added in C++11. I initially thought it made things better, but the more I learned, the more I realized that it caused other subtle issues and it became a tradeoff between which set of issues you prefer having to deal with, though it really only added issues, since it didn't remove anything from the language which would reduce complexity.  
  
Anyways, I'd rather not debate C++ features or proposed features. My point was that a consistent interface is possible and wouldn't be a stretch to implement. I'm not sure that it's the best interface. But it definitely is possible.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-01 03:02:50 UTC  
> Url: https://github.com/boostorg/beast/issues/122#issuecomment-326475545  

I'm going to go ahead and close this for now. When the HTTP/2 design work starts, we can open new issues - thanks!
