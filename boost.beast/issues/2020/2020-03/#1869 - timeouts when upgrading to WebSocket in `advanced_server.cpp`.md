# #1869 - [Question] timeouts when upgrading to WebSocket in `advanced_server.cpp` [Closed]

> Username: jcmonnin  
> Created at: 2020-03-02 03:15:06 UTC  
> Updated at: 2020-03-02 06:21:03 UTC  
> Closed at: 2020-03-02 06:21:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1869  

The [documentation](https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/using_websocket/timeouts.html) states:  
  
> The timeouts on the websocket stream are incompatible with the timeouts used in the tcp_stream. When constructing a websocket stream from a tcp stream that has timeouts enabled, the timeout should be disabled first before constructing the websocket stream, as shown.  
  
```  
// Disable any timeouts on the tcp_stream  
sock.expires_never();  
  
// Construct the websocket stream, taking ownership of the existing tcp_stream  
stream<tcp_stream> ws(std::move(sock));  
```  
The [advanced_server.cpp](https://www.boost.org/doc/libs/1_72_0/libs/beast/example/advanced/server/advanced_server.cpp) example calls in `do_read`:  
```  
        // Set the timeout.  
        stream_.expires_after(std::chrono::seconds(30));  
```  
however I don't see any call to `expires_never` before the creating the web socket stream.  
  
Is there a reason the `expires_never` can be omitted or is it a bug in the example?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-03-02 05:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1869#issuecomment-593226937  

> Is there a reason the expires_never can be omitted or is it a bug in the example?  
  
Neither. The `websocket::stream` is constructed from a `tcp::socket` not a `tcp_stream`. The `tcp_stream` used for the HTTP part of the session is destroyed, and a new one is constructed from the moved from `tcp::socket`.

---

## Comment 2

> Username: jcmonnin  
> Created at: 2020-03-02 06:21:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1869#issuecomment-593240010  

Thanks for the reply. I see now why it works.
