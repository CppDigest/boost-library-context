# #2928 - Websocket continuation frames dropped if there are leftovers in the buffer from the previous read [Closed]

> Username: pyrist  
> Created at: 2024-09-11 10:15:37 UTC  
> Updated at: 2024-09-12 13:07:18 UTC  
> Closed at: 2024-09-12 13:07:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2928  

Beast version used: 1.85  
  
I'm dealing with an issue where websocket messages over 8192 bytes aren't read completely.  
  
For example, for a websocket message split into 3 continuation frames, the first two arrive and are read. On the third, the first part of the frame is read, and then the rest is ignored.  
  
In websocket/impl/read.hpp line 172, it checks for the frame header. At the beginning of the first two frames, this works as intended: No frame header is there yet in the buffer, so async_read_some is called in line 191, cont is set to true, and reading continues happily along.  
  
However, at the beginning of the third frame, parse_fh checks if there's anything in the buffer yet in line 706 in websocket/impl/stream_impl.hpp. The buffer it checks (impl.rd_buf) still has some stuff in it from the previous read, so it gets the frame header and doesn't call async_read_some. Because of this, cont is false and the loop is broken. The rest of the websocket message is then not processed.   
  
I'm using weird proprietary sockets so I sadly cannot provide code to help reproduce the problem, since the problem doesn't happen with Windows native sockets. I believe the reason why it hasn't been an issue with Windows sockets is that my weird sockets send a piece of the next frame as well in the previous packet.

---

## Comment 1

> Username: pyrist  
> Created at: 2024-09-12 13:07:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2928#issuecomment-2346236903  

Helped by @ashtum to understand the problem isn't in boost beast
