# #2054 - Should new beast::ssl_stream<beast::tcp_stream> be created for every http request for every http request I make in coroutine [Closed]

> Username: ScottYueqiang  
> Created at: 2020-08-17 10:57:43 UTC  
> Updated at: 2020-09-25 04:05:57 UTC  
> Closed at: 2020-09-25 04:05:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2054  

should new beast::ssl_stream<beast::tcp_stream> be created for every http request I make in coroutine?  
what is the best way to deal with beast::ssl_stream<beast::tcp_stream>

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-17 12:57:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2054#issuecomment-674864933  

> should new beast::ssl_streambeast::tcp_stream be created for every http request I make  
  
Not if your server supports HTTP1.1 (which it will). You can tell whether to close the connection by examining this property having received the response:  
  
https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/keep_alive.html  
  
If `keep_alive()` is `true`, then you may keep the connection open and simply issue a new request on it.  
  
> in coroutine?  
  
irrelevant.  
  
> what is the best way to deal with beast::ssl_streambeast::tcp_stream  
  
This question is open-ended. Can you be more specific?

---

## Comment 2

> Username: ScottYueqiang  
> Created at: 2020-08-18 01:54:19 UTC  
> Updated at: 2020-08-18 02:29:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2054#issuecomment-675203826  

> > should new beast::ssl_streambeast::tcp_stream be created for every http request I make  
>   
> Not if your server supports HTTP1.1 (which it will). You can tell whether to close the connection by examining this property having received the response:  
>   
> https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/keep_alive.html  
>   
> If `keep_alive()` is `true`, then you may keep the connection open and simply issue a new request on it.  
>   
> > in coroutine?  
>   
> irrelevant.  
>   
> > what is the best way to deal with beast::ssl_streambeast::tcp_stream  
>   
> This question is open-ended. Can you be more specific?  
  
  
I use the beast/example/http/client/coro-ssl/http_client_coro_ssl.cpp to test.  
I will send a lot of requests at the same time, each request starts a coroutine, and asynchronously sends and accepts in the coroutine, will there be data race if the same stream is used;   
what i hope is create one stream and do once connect and handshake, then use it to handle many other requests.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-20 13:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2054#issuecomment-677667532  

Hi @ScottYueqiang ,  
  
As with all asio stream objects, you may make one async write and one async read per stream at a time. If you have a batch of frames to send, you must queue them in your own queue and write them one by one. During the completion handler of one `async_write`, you can discard the data you have sent and then initiate the next `async_write`.  
  
Does this make sense?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-09-20 03:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2054#issuecomment-695613612  

This issue has been open for a while with no activity, has it been resolved?
