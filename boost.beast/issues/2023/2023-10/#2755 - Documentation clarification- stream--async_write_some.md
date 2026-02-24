# #2755 - Documentation clarification: stream::async_write_some [Closed]

> Username: ashtum  
> Created at: 2023-10-24 07:43:41 UTC  
> Updated at: 2023-10-26 05:27:00 UTC  
> Closed at: 2023-10-26 05:27:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2755  

As discussed in issue #1070, the documentation should cover the following points:  
  
- stream::async_write_some always completes by writing a complete and valid WebSocket frame (not a message).  
- It is the user's responsibility to mark the end of the message on subsequent calls to stream::async_write_some.  
- Ping/pong and close operations can be initiated while a stream::async_write_some operation is still in progress.  
  
Adding an example to illustrate the usage of stream::async_write_some would be beneficial.
