# #2773 - No websocket::stream::async_wait method [Closed]

> Username: ecorm  
> Created at: 2023-12-02 02:03:14 UTC  
> Updated at: 2023-12-02 04:01:29 UTC  
> Closed at: 2023-12-02 04:00:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2773  

### Version of Beast: Boost 1.83  
  
I need to implement "idle timeout" logic with websockets where it times out when there has been no read activity for a certain number of seconds.  
  
With Asio TCP sockets, I would simply use `tcpsocket.async_wait(asio::ip::tcp::socket::wait_read, handler)`, followed by `async_read` with the timing logic in between. See Asio's [async_wait docs here](https://www.boost.org/doc/libs/release/doc/html/boost_asio/reference/basic_stream_socket/async_wait.html).  
  
But there is no equivalent `beast::websocket::stream::async_wait` method.  
  
As as workaround, is it safe to call `websocket.next_layer().async_wait(asio::ip::tcp::socket::wait_read, handler)` before calling `websocket.async_read(...)` ?

---

## Comment 1

> Username: ecorm  
> Created at: 2023-12-02 02:09:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2773#issuecomment-1836994868  

Another workaround just came to mind: I can just do `websocket.async_read_some(...)` with a `limit` of a single byte.  
  
But I'd still like to know if `next_layer().async_wait(...)` is safe before calling `async_read` on a websocket `stream`.

---

## Comment 2

> Username: ecorm  
> Created at: 2023-12-02 03:19:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2773#issuecomment-1837021200  

> But I'd still like to know if `next_layer().async_wait(...)` is safe before calling `async_read` on a websocket `stream`.  
  
Nope, just tried it and it broke things. Was probably a bad idea anyway to manipulate the `next_layer()` socket while it was under `websocket::stream`'s ownership.

---

## Comment 3

> Username: ecorm  
> Created at: 2023-12-02 04:00:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2773#issuecomment-1837029505  

> Another workaround just came to mind: I can just do websocket.async_read_some(...) with a limit of a single byte.  
  
This works. Closing this issue as I ended up answering my own questions, but feel free to re-open if you think `websocket::stream` deserves an `async_read` member function similar to `asio::ip::tcp:stream`.
