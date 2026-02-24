# #1942 - Fix http async_read_some [Closed]

> Username: madmongo1  
> Created at: 2020-05-06 14:04:28 UTC  
> Updated at: 2020-08-20 13:27:55 UTC  
> Closed at: 2020-05-20 14:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1942  

refs #1677  
  
http::async_read_some / read_some should report the number of bytes consumed by the http parser.  
  
At present it does not, as async_read_some is implemented in terms of async_read_until, which reports the number of bytes consumed from the stream - which may well be more than are required to complete the parse.
