# #2311 - is it safe to dispatch after socket was closed? [Closed]

> Username: benstadin  
> Created at: 2021-09-14 14:53:19 UTC  
> Updated at: 2021-09-14 15:38:40 UTC  
> Closed at: 2021-09-14 15:38:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2311  

I have an async WebSocket session on the server side which can be forcefully closed (to make a long story short the reason for doing this is an application requirement to get rid of WebSocket sessions and actively close them when some sort of license has expired).   
  
The current flow is like this:  
  
1. The server may at any time decide to get rid of the session:  
  
```  
void WebsocketSession<Derived>::close() {  
    net::dispatch(net::bind_executor(derived().ws().get_executor(), [self = sharedFromBase()] { self->doStop(); }));  
}  
  
void WebsocketSession<Derived>::doStop(websocket::close_reason reason) {  
    // do not attempt to close the socket twice  
    if (_ec)  
        return;  
      
    _ec = net::error::operation_aborted;  
  
    if (_state == WS_STATE_HANDSHAKING || _state == WS_STATE_CONNECTED) {  
        beast::close_socket(beast::get_lowest_layer(derived().ws()));  
    } else if (_state == WS_STATE_CLOSING) {  
        // no-op  
    }  
    _state = WS_STATE_CLOSING;  
}  
```  
  
2. All async methods called by the strand executor respect this closure flag and bail out. For example:  
  
```  
void WebsocketSession<Derived>::onRead(beast::error_code ec, std::size_t) {  
    if (_ec) {  
        Log(Info) << "The websocket connection was closed intentionally.";  
        return;  
    }  
    ....  
}  
```  
  
That does seem to work fine and all tests pass currently. My question however is whether it's safe to call dispatch after the socket was closed but before the session is removed internally:  
  
```  
// Dispatch message  
void WebsocketSession<Derived>::send(Message m) {  
    net::dispatch(derived().ws().get_executor(), beast::bind_front_handler(&WebsocketSession::doSend, sharedFromBase(), std::move(m)));  
}  
  
// Bail out of doSend in case socket was marked as closed:  
void WebsocketSession<Derived>::doSend(Message m) {  
    if (_ec || _state != WS_STATE_CONNECTED) {  
        return;  
    }  
    ...  
}  
```  
  
What is the expected behavior when calling dispatch after the socket was closed? Is it safe to assume that the executor just calls doSend(), or will it crash because the socket was closed? In that case I'd safeguard _ec, though I want to avoid locks if possible.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-09-14 15:24:47 UTC  
> Updated at: 2021-09-14 15:26:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2311#issuecomment-919258103  

As long as the websocket::stream object still exists, it's safe to use its executor as you are doing here.  
  
The `dispatch()` simply dispatches work to the executor. It could be any work, not necessarily to do with the websocket stream state.  
  
It is even safe to initiate another async operation against the websocket stream while the underlying socket is closed. All that will happen is that the completion handler will be scheduled for execution with an error indicated in the `error_code` argument.

---

## Comment 2

> Username: benstadin  
> Created at: 2021-09-14 15:38:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2311#issuecomment-919270562  

Thanks for the swift response, as always.
