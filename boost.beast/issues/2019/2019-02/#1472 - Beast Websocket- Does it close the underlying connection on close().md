# #1472 - Beast Websocket: Does it close the underlying connection on close() [Closed]

> Username: emag37  
> Created at: 2019-02-22 14:58:07 UTC  
> Updated at: 2019-02-22 15:24:47 UTC  
> Closed at: 2019-02-22 15:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1472  

Hello,  
  
We are currently using Boost::Beast websockets over SSL. I had a question regarding the `close()` and `async_close()` functions in Beast:   
  
The documentation seems to indicate that if you `close()` the websocket layer, it will also gracefully shutdown the underlying SSL, TCP ASIO layers. Is this true of `async_close()`? I get the callback immediately with a `success` error code, but the underlying connection still seems to be open (`lowest_layer().is_open()` is still `true`). Do I have to `read()` until I get a `websocket::closed` error code?  
  
Right now we are forcing the shutdown of each layer in the `async_close()` handler, which works well, but I was wondering what the 'correct' way is. There seems to be some old Stack Overflow posts indicating that one must manually shutdown SSL/TCP after calling websocket close...is this still the case?  
  
Thank you.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-22 14:59:09 UTC  
> Updated at: 2019-02-22 15:00:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1472#issuecomment-466425346  

`close`  and `async_close` work the same way. Yes you have to `read` or `async_read` to get the `error::closed`. This is explained in the docs  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/control_frames.html#beast.using_websocket.control_frames.close_frames  
  
Yes, Beast closes the underlying socket for you, see:  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/teardown.html

---

## Comment 2

> Username: emag37  
> Created at: 2019-02-22 15:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1472#issuecomment-466434176  

Alrighty, thank you. I guess I got a bit confused because I remember reading that you had to drain the buffer with read() commands, but then in the new version you don't have to do that anymore, except I guess you still need read() commands to make sure it's closed...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-22 15:24:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1472#issuecomment-466434596  

> I remember reading that you had to drain the buffer with read() commands  
  
Yeah that was how it worked originally, but feedback from users suggested it would be much nicer if the library did it (and I think we all agree on that)
