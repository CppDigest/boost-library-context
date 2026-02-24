# #1715 - Websocket data contains extra bytes [Closed]

> Username: benstadin  
> Created at: 2019-10-03 03:06:22 UTC  
> Updated at: 2019-10-03 13:55:28 UTC  
> Closed at: 2019-10-03 13:55:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1715  

I have an issue in Safari and Chrome to parse a json string sent by the server (using Boost 1.71). JSON.parse() always throws an error complaining the string couldn't be parsed (e.g. "unrecognized ''", even though the json looks valid.   
  
Splitting the string on the client side into a char array shows the string contains extra bytes after null. At the moment I only guess this might be the problem. Is there something I could do about this, or is this an issue in Beast?  
  
This is the send method used:  
  
```  
template<class Derived>  
void WebsocketSession<Derived>::send(const std::string &msg) {  
    buffer_.consume(buffer_.size());  
                buffer_.commit(net::buffer_copy(  
                                                 buffer_.prepare(msg.size()), net::buffer(msg.data(), msg.size())));  
     
      
    net::dispatch(derived().ws().get_executor(),  
                  beast::bind_front_handler(&WebsocketSession::doSend,  
                                            sharedFromBase(),  
                                            buffer_.data()));  
}  
  
template<class Derived>  
void WebsocketSession<Derived>::doSend(Message m) {  
    if(! beast::get_lowest_layer(derived().ws()).socket().is_open())  
        return;  
    queue_.emplace_back(std::move(m));  
     
    if(queue_.size() == 1)  
        doWrite();  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-03 10:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1715#issuecomment-537883685  

Impossible, beast always sends exactly what you give it. None of the tests would pass if this wasn't the case. Do you have compression turned on?

---

## Comment 2

> Username: benstadin  
> Created at: 2019-10-03 11:23:44 UTC  
> Updated at: 2019-10-03 11:24:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1715#issuecomment-537903852  

Yes. Though I did not enable it actively on the server side. I see there could be a (resolved) related issue with compression?  
  
It works when the extra null is removed on the client side. FWIW this is the client code:  
  
  
```  
              var socket = new WebSocket('ws://localhost:8081/v1/ws/sub/locationupdates?geojson=true');  
                socket.onopen = function() {  
                    console.log("Connection to locationupdates ws established");  
                };  
                socket.onmessage = function (evt) {  
                    console.log("Sent: '" + evt.data.split('') + "'");  
                    var geojsonStr = evt.data.replace(/\0[\s\S]*$/g,'');  
                    console.log("Cleaned: '" + geojsonStr.split('') + "'");  
                    var geojson = JSON.parse(geojsonStr);  
                    map.getSource('drone').setData(geojson);  
                };  
                socket.onclose = function() {  
                    console.log("Connection to locationupdates ws closed");  
                };  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-03 12:34:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1715#issuecomment-537925500  

I don't think there is a problem with the permessage-deflate (compression) but if you haven't enabled it on the server then permessage-deflate is not negotiated even if the client requests it. I am still skeptical in the extreme that beast has even the slightest whiff of a bug where there could be bytes when it sends websocket messages. There are just too many people using it, and too much comprehensive test coverage, for something like this to be unnoticed all this time. Mind you that beast passes the autobahn test suite.  
  
What is the type of `Message`? Are you also using `buffer_` for reading?

---

## Comment 4

> Username: benstadin  
> Created at: 2019-10-03 13:41:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1715#issuecomment-537950651  

Thanks, you pointed me to the right direction.   
  
buffer_ is essentially unused. Once the connection is established the server only pushes messages to the client. All messages from the client are just consumed.   
  
The problem was with creating the Message object. I reused the message class from your BeastLounge project. The only difference is that I changed it to create a Message from a string:  
  
```  
Message lotus::web::server::makeMessage(const std::string &msg) {  
    return Message(net::const_buffer(msg.c_str(), msg.length() + 1));  
}  
```  
  
Intuitively I added +1 to the buffer size. Does it make sense to handle the case gracefully when there is a null terminated string in the buffer?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-10-03 13:45:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1715#issuecomment-537952335  

Try this  
```  
message make_message(std::string const& s)  
{  
  return message(boost::asio::buffer(s));  
}  
```

---

## Comment 6

> Username: benstadin  
> Created at: 2019-10-03 13:55:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1715#issuecomment-537956360  

Thanks, works and looks cleaner. I'll close this issue.
