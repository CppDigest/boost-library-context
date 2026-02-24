# #322 - Need a close_callback and socket_state options [Closed]

> Username: openvoipco  
> Created at: 2017-04-26 03:05:51 UTC  
> Updated at: 2017-06-25 15:33:47 UTC  
> Closed at: 2017-06-25 15:33:47 UTC  
> Url: https://github.com/boostorg/beast/issues/322  

Hello, I am sorry if it was discussed and I missed it  
  
**I am looking for the following functionality:**  
1. Need a "close_callback" option (ping_callback alike) - should be triggered when a websocket close happens by any reason (initiated by any side). Interface could be something like "void(close_code::value)". I see multiple places in the read.ipp, but cause there are multiple places where close might be initiated - would be good to get your suggestions about it.  
  
2. Need a "socket_state" option getter to get the current websocket layer state. Looks like it can be extracted from "d_" member var. So it can be easy to understand the websocket is (at least) connected or disconnected.  
  
I also assume that there is a way to achieve the same without making any changes. Please help...  
  
Thank you  
Roman

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-26 17:12:02 UTC  
> Updated at: 2017-04-26 17:12:58 UTC  
> Url: https://github.com/boostorg/beast/issues/322#issuecomment-297479332  

1. You want to know when the local endpoint initiates a close? That's entirely under the application's control (call `stream::close` or `stream::async_close`). I think you mean, that you want to know when the remote endpoint initiates a close by sending a close frame? Or do you want to know when both sides have sent a close frame (meaning the session is ended)?  
  
How about if we replace ping and pong callbacks with a generic "control frame callback" which gets called for all received control frames (ping, pong, and close)? That would simplify the interface.   
  
Check out this issue: https://github.com/vinniefalco/Beast/issues/209  
  
2. How about this new stream member function:  
```  
/** Returns `true` if the WebSocket session is established.  
  
    If asynchronous operations are being performed, this function must be  
    called from the same implicit or explicit strand or else the return value  
    is undefined.  
*/  
bool  
is_open() const;  
```  
  
I think changes will be required to accomplish your goal. If you could give me more information about your use-case I can make sure you are accommodated.

---

## Comment 2

> Username: openvoipco  
> Created at: 2017-04-26 17:26:01 UTC  
> Url: https://github.com/boostorg/beast/issues/322#issuecomment-297483164  

Hello, thank you for quick feedback!  
  
1. Yes, this one "you want to know when both sides have sent a close frame". The "control frame callback" is also a good thing, but I would also trigger a "close" (for example) event in cases when the WebSocket is being closed by internal causes as well, like underlying layer socket is closed or whatever. For example when I use async_write and it internally understands that there is no TCP anymore - it triggers this callback as well. This event should notify user that WebSocket connection is unavailable anymore...  
  
2. This is great!  
  
I found these needs when I started wrapping my websocket code into a small client class with a simple interface. I use signals2, so I can attach any number of callbacks to events like on_data, on_close, etc. So I need a way to easily control connection state, get notified about it. If we would have this kind of callback logic - it would be easy to do reconnects in advance (not when "read/write" returns error)...  
  
  
Thank you  
Roman

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-08 20:07:29 UTC  
> Url: https://github.com/boostorg/beast/issues/322#issuecomment-307212847  

@openvoipco Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!
