# #2547 - How to correctly immediately close websocket stream [Closed]

> Username: gunzino  
> Created at: 2022-10-21 09:02:50 UTC  
> Updated at: 2022-10-21 09:35:32 UTC  
> Closed at: 2022-10-21 09:35:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2547  

Hello,  
  
I've managed to implement beast to my software, However I experience some rare crashes due to assertion in stream_base.hpp (line 86) when client lose the network connection.   
  
My disconnect function looks like this:  
```  
void ProxyWebsocket::disconnect()  
{  
    if (m_state == STATE_CLOSING || m_state == STATE_NOT_CONNECTED) {  
        return;  
    }  
    auto self = getThis();  
    m_state = STATE_CLOSING;  
    boost::beast::get_lowest_layer(*(self->ws_)).close();  
    ws_->async_close(boost::beast::websocket::normal, [self](const boost::system::error_code& ec) {  
        self->m_state = STATE_NOT_CONNECTED;  
    });  
}  
```  
  
The disconnect function is called by the program when there is 2s timeout. Then it should attempt to establish new connection. After async_close is handled and state is set to STATE_NOT_CONNECTED the object declare new ws_ using std::optional::emplace but in very rare situation it seems like the program is searching for deleted ws_ object. If I'm correct the async_close should ensure that other handlers are gonna be called. Should I use different function to close the stream?  
  
Is there difference between get_lowest_layer(*(self->ws_)).close() and get_lowest_layer(*(self->ws_)).socket().close() ?  
  
Thanks !!

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-21 09:11:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2547#issuecomment-1286682913  

Which assertion fires? and why are you calling async_close on the websocket after closing the underlying socket?

---

## Comment 2

> Username: gunzino  
> Created at: 2022-10-21 09:19:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2547#issuecomment-1286690645  

The assertion is beast/core/detail/stream_base.hpp line 86. That's good question, probably I wanted to be sure that operation handlers are cancelled so ws_ object could be deleted by std::optional.

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-10-21 09:26:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2547#issuecomment-1286698270  

If you're keeping track of the connection state (e.g. m_state), it's likely that there is a design error.  
With asio io objects you know you're still connected because your read last succeeded. If that fails, this is your signal that you've transitioned to "not connected" state.

---

## Comment 4

> Username: gunzino  
> Created at: 2022-10-21 09:35:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2547#issuecomment-1286708326  

@madmongo1   
Seems that could be the issue, I didn't check the error_code in one of the handlers and just called next async_read_some.
