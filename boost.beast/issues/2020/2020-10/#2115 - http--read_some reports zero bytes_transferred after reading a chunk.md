# #2115 - http::read_some reports zero bytes_transferred after reading a chunk [Closed]

> Username: madmongo1  
> Created at: 2020-10-28 07:33:32 UTC  
> Updated at: 2024-06-06 05:34:21 UTC  
> Closed at: 2020-10-29 06:37:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2115  

refs #2111   
  
Chunked encoding.   
Use http::read_header to consume the header  
then use http::read_some to consume each chunk.  
Reads of a chunk body report zero bytes transferred.  
  
Use case: use `http::response<http::buffer_body>` in order to create a stream type which can be composed with asio `async_read[_some`] etc.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-10-29 06:37:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2115#issuecomment-718394519  

False alarm.
