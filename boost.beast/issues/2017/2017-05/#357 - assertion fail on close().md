# #357 - assertion fail on close() [Closed]

> Username: redboltz  
> Created at: 2017-05-12 09:22:50 UTC  
> Updated at: 2017-05-13 03:49:57 UTC  
> Closed at: 2017-05-13 03:49:57 UTC  
> Url: https://github.com/boostorg/beast/issues/357  

I am developing websocket server using Beast.  
  
When my server calls [close()](http://vinniefalco.github.io/beast/beast/ref/websocket__stream/close.html), I sometimes got assertion fail on the following line.  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/close.ipp#L235  
  
It happens the following case:  
* Client is web browser. (Chrome on Windows).  
* Network is disconnected.  
  
I read the following description.  
http://vinniefalco.github.io/beast/beast/websocket/control.html#beast.websocket.control.close_frames  
  
But I'm not sure how to handle this assertion failed.  
  
It seems that the server shouldn't call close() if the stream already sent WebSocket close frame.  
However, how to check it?  
As far as I have checked, I don't call close() twice.   
  
wr_close_ is set at   
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/close.ipp#L157  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read.ipp  
  
The server uses async_read and async_write. When their callback called with error, should I call close() or not?  
  
I'd like to know when I should call close(), and when I shoudn't call it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-12 20:44:30 UTC  
> Url: https://github.com/boostorg/beast/issues/357#issuecomment-301181628  

You should only call `close` when:  
* The stream is connected  
* You have no more pending writes  
* You want the WebSocket session to end  
  
If you get an error on any I/O, you should not call `close`.

---

## Comment 2

> Username: redboltz  
> Created at: 2017-05-13 03:49:57 UTC  
> Url: https://github.com/boostorg/beast/issues/357#issuecomment-301222998  

@vinniefalco , thank you for the comment. I understand.
