# #2260 - Force closing a WebSocket connection with a pending read [Closed]

> Username: benstadin  
> Created at: 2021-06-14 17:57:12 UTC  
> Updated at: 2021-06-14 23:20:23 UTC  
> Closed at: 2021-06-14 23:20:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2260  

I've some sort of timer based logic (trial period) in my application. After the trial period expired all WebSocket Sessions shut be forcefully shut down. The session is however never closed and destroyed at the moment. My guess is so far that the problem is that I have a read pending in my on_read:   
  
```  
        // Read another message  
        derived().ws().async_read(  
                                  buffer_,  
                                  beast::bind_front_handler(&WebsocketSession::onRead,  
                                                     sharedFromBase()));  
```  
  
Is there a way to force closing a connection with a pending read? If that is the actual issue, anyways.  
  
My close connection method is implemented as follows:  
  
```  
template<class Derived>  
void WebsocketSession<Derived>::close() {  
      
    // removes the session from mediator and all subscribed topics.  
    if (getMediator()) {  
        getMediator()->removeUserSession(this);  
    }  
      
   net::post(derived().ws().get_executor(),  
              beast::bind_front_handler(&WebsocketSession::doStop,  
                                        sharedFromBase()));  
}  
  
template<class Derived>  
void WebsocketSession<Derived>::doStop() {  
    beast::close_socket(beast::get_lowest_layer(derived().ws()));  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-06-14 18:01:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2260#issuecomment-860881365  

If you close the underlying socket, all outstanding operations (including read) will complete immediately with `net::error::operation_aborted`. Make sure you call `close` from the strand (implicit or explicit).

---

## Comment 2

> Username: benstadin  
> Created at: 2021-06-14 23:20:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2260#issuecomment-861055386  

Thanks, works now. I've also found this example to be helpful:   
https://github.com/test-scenarios/boost_beast_websocket_echo/blob/master/pre-cxx20/echo_server/connection.cpp
