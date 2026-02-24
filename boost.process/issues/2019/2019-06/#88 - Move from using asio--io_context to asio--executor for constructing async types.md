# #88 - Move from using asio::io_context to asio::executor for constructing async types [Open]

> Username: complexmath  
> Created at: 2019-06-05 22:02:12 UTC  
> Updated at: 2024-02-19 20:38:10 UTC  
> Url: https://github.com/boostorg/process/issues/88  

boost::asio has been moving away from using io_context for initialization to the more generally defined executor type, and in 1.70 the deprecated socket.get_io_context() is gone entirely. As boost::process remains unchanged in this regard, code that relied on being able to get the io_context from sockets and such as a means of initializing async_pipes no longer compiles. Consider moving towards accepting an executor instead.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-06-06 04:48:10 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-499346800  

What code part relies on getting an `io_context` from sockets?

---

## Comment 2

> Username: complexmath  
> Created at: 2019-06-06 21:07:43 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-499668055  

async_pipe is initialized via an io_context. In server code, for example, if I want to initialize an async_pipe while processing a client request, and I want to be sure that pipe is initialized with the same io_context the socket is using, so events serialize properly, the most practical approach is to use socket.get_io_context(). Except this is gone now.  
  
Since boost::asio is moving away from using io_context towards executors in general though, it seems like boost::process should follow suit, if for no other reason than to do things in the intended manner. I can only guess that asio is moving towards having different executor specializations for different uses.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-06-07 03:17:23 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-499743012  

Ah ok, well that changes to `socket.get_executor().context()`.   
  
Thing is: `async_pipe` uses the [`posix::stream_handle`](https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/posix__basic_stream_descriptor/basic_stream_descriptor.html) or the windows equivalent under the hood. They still take a context.  
  
I feel like changing this for `async_pipe` would break a lot of peoples code.

---

## Comment 4

> Username: complexmath  
> Created at: 2019-06-07 17:58:03 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-499981065  

`socket.get_executor().context()` returns a different type which is a parent class (or template sibling maybe) of io_context. I'd be fine passing that in instead of an executor. I just don't want to have to try and downcast to io_context, which is what I'd need to do today.

---

## Comment 5

> Username: amerry  
> Created at: 2021-10-01 16:41:46 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-932384284  

> Thing is: async_pipe uses the posix::stream_handle or the windows equivalent under the hood. They still take a context.  
  
According to the link you gave, they take an executor (like the other ASIO types, they can also take an io_context for backwards compatibility, but the documented API pushes you towards using an executor).

---

## Comment 6

> Username: maxtruxa  
> Created at: 2023-08-31 14:48:18 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-1701194023  

This is really annoying when working with coroutines, where you can easily retrieve the active executor from `this_coro::executor` but there is no way to get back to an `io_context` from that.  
```cpp  
namespace asio = boost::asio;  
namespace bp = boost::process;  
  
asio::awaitable<void> something() {  
    auto executor = co_await asio::this_coro::executor;  
    asio::steady_timer t{executor}; // example  
    bp::async_pipe pipe{???}; // ...  
}  
```  
The workaround is obviously to pass a reference to the `io_context` around, but that is now *only* necessary for Boost.Process and super annoying when it's deep within the application.  
```cpp  
asio::awaitable<void> something(asio::io_context& ioc) {  
    bp::async_pipe pipe{ioc}  
}  
```

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-08-31 15:09:03 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-1701230729  

I developed process.v2 since the this issue was opened, so that should help. Note that asio has pipes now as well, in case you don't need processes.

---

## Comment 8

> Username: maxtruxa  
> Created at: 2023-09-01 11:58:11 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-1702633473  

Oh, somehow I assumed "v2" is what's in the `v2` branch (which has the same issue) without looking at the docs... My bad. V2 works a treat!

---

## Comment 9

> Username: rhapsodyv  
> Created at: 2024-02-19 20:38:09 UTC  
> Url: https://github.com/boostorg/process/issues/88#issuecomment-1953131520  

I was having the same issue and this worked for me:  
  
```cpp  
boost::asio::io_context io;  
boost::asio::any_executor ex(io);  
....  
....  
boost::asio::io_context &io_context = ex.target<boost::asio::io_context::executor_type>()->context();  
```
