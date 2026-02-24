# #2885 - HTTP example uses explicit strand : why [Closed]

> Username: pfeatherstone  
> Created at: 2024-06-11 08:03:16 UTC  
> Updated at: 2024-07-04 16:33:11 UTC  
> Closed at: 2024-07-04 16:33:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2885  

In https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp the `session` object uses an explicit strand. Why is this the case?  
  
The [Asio documentation](https://live.boost.org/doc/libs/1_85_0/doc/html/boost_asio/overview/core/strands.html) says:  
> Where there is a single chain of asynchronous operations associated with a connection (e.g. in a half duplex protocol implementation like HTTP) there is no possibility of concurrent execution of the handlers. This is an implicit strand.   
  
So why do we need an explicit strand?  
  
Cheers  
  
@vinniefalco @klemens-morgenstern

---

## Comment 1

> Username: ashtum  
> Created at: 2024-06-11 08:54:09 UTC  
> Updated at: 2024-06-11 09:19:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2885#issuecomment-2160161675  

The [beast::tcp_stream](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__tcp_stream.html) allows for specifying a timeout on the stream, which internally initiates a parallel `async_wait` on a timer for each asynchronous operation on that stream. Here is where a timeout is set in that specific example: https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp#L252.  
  
Aside from that, in multi-threaded examples, not using a strand can lead to mistakes and concurrency bugs when users modify the examples for their purposes. These bugs are often silent and difficult to debug.

---

## Comment 2

> Username: pfeatherstone  
> Created at: 2024-06-11 10:41:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2885#issuecomment-2160425513  

So i'm not using `beast::tcp_stream` just a normal `tcp_socket`. I don't need timers. So in my case, i have an implicit strand right?

---

## Comment 3

> Username: pfeatherstone  
> Created at: 2024-06-11 10:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2885#issuecomment-2160427791  

Is it good practice in Asio/Beast to disregard the fact that you have an implicit strand and just use an explicit one anyway ? Doesn't that incur a performance overhead?

---

## Comment 4

> Username: ashtum  
> Created at: 2024-06-11 11:04:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2885#issuecomment-2160466277  

> So i'm not using beast::tcp_stream just a normal tcp_socket. I don't need timers. So in my case, i have an implicit strand right?  
  
Yes, If you design your application in a way that guarantees all operations on an I/O object (like `asio::ip::tcp::socket`) are sequential and **no mutations occur after calling each initiating function**, then you don't need a strand because there is no way to have two in-flight completion handlers.  
  
> Is it good practice in Asio/Beast to disregard the fact that you have an implicit strand and just use an explicit one anyway ? Doesn't that incur a performance overhead?  
  
I would say the overhead is insignificant compared to the actual task scheduled for execution, so performance-wise, it doesn't make a difference in most applications.

---

## Comment 5

> Username: pfeatherstone  
> Created at: 2024-06-11 11:16:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2885#issuecomment-2160486937  

Yeah safety by default is probably a good thing.
