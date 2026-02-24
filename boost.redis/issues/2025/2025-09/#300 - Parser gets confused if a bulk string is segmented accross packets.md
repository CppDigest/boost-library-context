# #300 - Parser gets confused if a bulk string is segmented accross packets [Closed]

> Username: anarthal  
> Created at: 2025-09-05 17:25:55 UTC  
> Updated at: 2025-09-05 17:37:17 UTC  
> Closed at: 2025-09-05 17:37:17 UTC  
> Url: https://github.com/boostorg/redis/issues/300  

[This check](https://github.com/boostorg/redis/blob/0cf2441ed2b81506f4b6f626d01ea43785ba31a9/include/boost/redis/resp3/impl/parser.ipp#L76) is wrong, since it's only valid if the bulk header and the body are in the same packet.  
  
For instance:  
  
"$11\r\nhello world" => consumes the header (5 bytes)  
"hello world\r\n" => returns needs_more, when it shouldn't  
  
Found while adding extra coverage to the multiplexer. Will fix as part of that PR.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-09-05 17:37:17 UTC  
> Url: https://github.com/boostorg/redis/issues/300#issuecomment-3259237561  

I've misinterpreted the code in reader_fsm and was simulating the wrong thing. There is no such an issue.
