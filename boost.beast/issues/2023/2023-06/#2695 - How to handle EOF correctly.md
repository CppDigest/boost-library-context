# #2695 - How to handle EOF correctly? [Closed]

> Username: benstadin  
> Created at: 2023-06-05 12:39:30 UTC  
> Updated at: 2023-10-28 16:33:21 UTC  
> Closed at: 2023-10-28 16:33:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2695  

We've been reported some issues where our backend does no longer handle websocket connections. We have not found a way to reproduce the issue yet, though the problem is always accompanied with an "End of file" error in the log.   
  
Therefore the question: is our EOF handling correct?  
  
This are our async onRead, fail and close methods:  
  
```  
template <class Derived>  
void WebsocketSession<Derived>::onRead(beast::error_code ec, std::size_t) {  
    if (_ec) {  
        hdmLog(Info) << "The websocket connection was closed by doStop while attempting to read.";  
        return;  
    }  
  
    // This error means the other side  
    // closed the websocket stream.  
    if (ec == websocket::error::closed) {  
        close();  
        return;  
    }  
      
    // Handle any other error  
    if (ec) {  
        close();  
        return fail(ec, "read");  
    }  
  
    if (_controller) {  
        _controller->onRead(buffer_, this);  
    } else {  
        // no controller handles this session. just clear the buffer.  
        buffer_.consume(buffer_.size());  
    }  
  
    // Read another message  
    derived().ws().async_read(buffer_, beast::bind_front_handler(&WebsocketSession::onRead, sharedFromBase()));  
}  
  
template <class Derived>  
void WebsocketSession<Derived>::fail(beast::error_code ec, char const* what) {  
   if (ec == net::error::operation_aborted || ec == websocket::error::closed || ec == net::error::not_connected || ec == net::error::eof)  
        return;  
  
    if (_address.length())  
        logWithIp(Error, _address) << what << ": " << ec.message();  
    else  
        log(Error) << what << ": " << ec.message();  
}  
  
template <class Derived>  
void WebsocketSession<Derived>::close() {  
    net::dispatch(net::bind_executor(derived().ws().get_executor(), [self = sharedFromBase()] { self->doStop(); }));  
}  
  
template <class Derived>  
void WebsocketSession<Derived>::doStop(websocket::close_reason reason) {  
    // do not attempt to close the socket twice  
    if (_ec)  
        return;  
  
    // we set an error code in order to handle the crossing case where the  
    // accept has completed but its handler has not yet been scheduled for  
    // invoacation  
    _ec = net::error::operation_aborted;  
  
    if ((_state == WS_STATE_HANDSHAKING || _state == WS_STATE_CONNECTED) && beast::get_lowest_layer(derived().ws()).socket().is_open()) {  
        beast::close_socket(beast::get_lowest_layer(derived().ws()));  
    } else if (_state == WS_STATE_CLOSING) {  
        // no-op  
    }  
  
    _state = WS_STATE_CLOSING;  
    removeSession();  
}  
  
template <class Derived>  
void WebsocketSession<Derived>::onAccept(beast::error_code ec) {  
    if (!_address.length() && beast::get_lowest_layer(derived().ws()).socket().is_open()) {  
        _address = beast::get_lowest_layer(derived().ws()).socket().remote_endpoint().address().to_string();  
        unsigned short port = beast::get_lowest_layer(derived().ws()).socket().local_endpoint().port();  
        if (port) {  
            _address.append(":");  
            _address.append(std::to_string(port));  
        }  
    }  
  
    if (_ec) {  
        // we've been stopped in the meantime  
        return;  
    }  
  
    // Handle the error, if any  
    if (ec) {  
        // force closure on any other error  
        close();  
        return fail(ec, "accept");  
    }  
  
    _state = WS_STATE_CONNECTED;  
  
    // Add this session to the list of active sessions  
    if (_controller) {  
        _controller->onAccept(this);  
    }  
  
    // Read a message  
    derived().ws().async_read(buffer_, beast::bind_front_handler(&WebsocketSession::onRead, sharedFromBase()));  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-05 13:54:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1576851614  

It looks correct on first glance - does it usually work?

---

## Comment 2

> Username: benstadin  
> Created at: 2023-06-05 14:07:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1576877887  

Yes, it usually works. We see this only with a certain external client and the EOF.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-06-06 06:27:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1577990021  

Do you know if he closes gracefully or just closes the socket?

---

## Comment 4

> Username: benstadin  
> Created at: 2023-06-06 08:33:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1578187570  

I do not know, but I have not seen this issue with any other client yet, making me think it may not close the connection gracefully. Our server runs 24/7 without issues usually.

---

## Comment 5

> Username: benstadin  
> Created at: 2023-06-06 13:51:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1578809525  

Is it legal to schedule another read after EOF?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-06-07 03:03:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1579797146  

No, but EOF and WS close are different things.  
  
Websocket is a protocol on top of tcp; if the TCP shuts down you get either a broken_pipe or an EOF error which is at the socket, not websocket level.  
  
Do you have any logs of the error code caused by this client?

---

## Comment 7

> Username: benstadin  
> Created at: 2023-06-07 10:06:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1580377512  

I requested the client logs, but do not have them yet. If the TCP session shuts down on EOF or borken_pipe, then why does beast not set ec to websocket::error::closed? From the backend logs (the only meaningful there is "read: End of file") I can verify that this is not the case at that point:  
  
```  
    if (ec == websocket::error::closed) {  
        close();  
        return;  
    }  
      
    // Handle any other error  
    if (ec) {  
        close();  
        return fail(ec, "read"); // bails out here on EOF  
    }  
```

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2023-06-07 12:04:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1580671524  

Because it's a different error. Closes means the websocket was closed when you initiated the read, EOF means the client send an EOF (which is invalid for the WS protocol) and broken_pipe means the client just closed the connection. Which boost version are you working with?

---

## Comment 9

> Username: benstadin  
> Created at: 2023-06-08 17:33:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1583071037  

Thanks for the clarification. We are using Boost version 1.81.

---

## Comment 10

> Username: ashtum  
> Created at: 2023-08-19 05:05:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2695#issuecomment-1684818818  

@benstadin Is this still open?
