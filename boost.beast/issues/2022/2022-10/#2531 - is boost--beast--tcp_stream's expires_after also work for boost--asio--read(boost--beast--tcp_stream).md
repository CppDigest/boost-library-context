# #2531 - is boost::beast::tcp_stream's expires_after also work for boost::asio::read(boost::beast::tcp_stream) [Closed]

> Username: xtayaitak  
> Created at: 2022-10-08 13:59:37 UTC  
> Updated at: 2022-10-08 15:19:06 UTC  
> Closed at: 2022-10-08 15:19:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2531  

beast::tcp_stream has async_read_some function buy But has no async_read function.  
so I use boost::asio::read for beast::tcp_stream。  
I don't know if the timeout and rate limit also work .

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-08 15:18:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2531#issuecomment-1272341090  

It works if you use asio::async_read. The sync versions don't support timeouts.
