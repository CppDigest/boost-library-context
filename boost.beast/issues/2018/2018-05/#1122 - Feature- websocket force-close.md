# #1122 - Feature: websocket force-close [Open]

> Username: vinniefalco  
> Created at: 2018-05-09 16:30:05 UTC  
> Updated at: 2021-06-15 22:39:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1122  

In some cases it may be useful to have an implementation of `close` and `async_close` which simply sends the close frame at the earliest opportunity and then performs the teardown without waiting for any pending incoming messages to complete.

---

## Comment 1

> Username: onto  
> Created at: 2018-10-19 05:30:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-431249341  

There would be useful method that just send close frame and nothing else. Then we can get `websocket::error::closed` on read incoming messages.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-10-19 13:15:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-431359410  

Is this what you want? https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html

---

## Comment 3

> Username: onto  
> Created at: 2018-10-29 06:17:25 UTC  
> Updated at: 2018-10-29 06:19:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-433799396  

If I correctly understand, `async_close` send close frame and then read from stream until close frame from other side is recieved, and only after that it call callback. In my suggestion I mean that `async_close` can optionally only send close frame and at next time when we immediately call `async_read` we got `websocket::error::closed` . It's not a force-close, more like deffred-close.

---

## Comment 4

> Username: chreniuc  
> Created at: 2019-03-13 14:37:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-472449086  

Hi @vinniefalco,   
I was just curious on how is this going as I noticed that this issue is 1 year old.  
  
This force call will send the close frame and it will close the socket without waiting for the close frame from the client, right?  
  
Right now when we detect that the client has disconnected ungracefully(lost the Internet connection) by using a timer and PING-PONG algorithm, we should call ` ws.next_layer().cancel();`, like you said [here](https://github.com/boostorg/beast/issues/214#issuecomment-269091213). If we would call `async_close` the connection would never close, because the client can't respond with the close frame.  
  
But if we would have this `async_force_close` we wouldn't need to call ` ws.next_layer().cancel();`, we would call `async_force_close` and this will close everything without waiting for a response from the client, right?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-03-13 14:47:04 UTC  
> Updated at: 2019-03-13 14:59:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-472453232  

> using a timer  
  
"idle ping" and "idle disconnect" timers are now built-in to the websocket stream so you can get rid of all that code and replace it with something like this:  
  
```  
ws.set_option(  
    websocket::stream_base::timeout::suggested(beast::role_type::server));  
```  
  
See: https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/timeouts.html

---

## Comment 6

> Username: chreniuc  
> Created at: 2019-03-18 15:57:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-473974440  

Thanks @vinniefalco, I managed to make this change in our code and it works like a charm.  
  
There were some issues updating from `1.69.0` to `1.70.0` to use `tcp_stream`, I had to replace some things:  
  
Older(1.69.0):  
```c++  
std::shared_ptr<boost::beast::websocket::stream<  
  boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>> ws{nullptr}  
```  
  
New(1.70.0):  
```c++  
std::shared_ptr<boost::beast::websocket::stream<  
  boost::beast::ssl_stream<boost::beast::tcp_stream>>> ws{nullptr};  
```  
  
___  
  
Older:  
```c++  
const boost::asio::ip::tcp::endpoint& endpoint =  
 connection->ws->lowest_layer().remote_endpoint();  
```  
  
New:  
```c++  
const boost::asio::ip::tcp::endpoint& endpoint =  
  connection->ws->next_layer().next_layer().socket().remote_endpoint();  
```  
  
___  
  
I also notice that you cannot create a strand like we did in version `1.69.0`(you get an error at compile time), ex:  
  
```c++  
std::shared_ptr<boost::beast::websocket::stream<  
  boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>> ws{nullptr}  
  
std::shared_ptr<boost::asio::strand<boost::asio::io_context::executor_type>> strand{nullptr};  
strand = std::make_shared<  
  boost::asio::strand<boost::asio::io_context::executor_type>>(  
  ws->get_executor());  
```  
  
We fixed that like you did in the advanced example, we removed the strand variables and used `make_strand`, I tried to create a skeleton of our code and show you the differences between 1.69.0 and 1.70.0. Maybe this will help others aswell: https://github.com/chreniuc/beast_1.69.0-to-1.70.0/commit/8dcdfb22d65277ebadb913fd8019886dfd556570  
  
In the advanced example you are using the same `io_context` for `acceptor` and for the complition handlers for reading and writing. We are using two different `io_context`s for these operations as you can see in that example and I was wondering if I created the strands correctly [here](https://github.com/chreniuc/beast_1.69.0-to-1.70.0/commit/8dcdfb22d65277ebadb913fd8019886dfd556570#diff-118fcbaaba162ba17933c7893247df3aL6).   
  
We want the `acceptor` to use an `io_context` and not use `strand`, but we want to use `strand`s when reading and writing. In `1.69.0` you would use `bind_executor` if you wanted to use `strand`s, but in `1.70.0` you don't use `bind_executor` even when you want to use `strand`s, you simply create a `tcp::socket(make_strand(io_context))`, right? So if I do not pass `make_strand` to the constructor of `acceptor` it should use strand, right?  
  
Thanks. :D

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-03-18 16:33:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-473993532  

You can use `beast::get_lowest_layer` to simplify  
```  
const boost::asio::ip::tcp::endpoint& endpoint =  
  connection->ws->next_layer().next_layer().socket().remote_endpoint();  
```  
into  
```  
auto const& ep = get_lowest_layer(connection->ws).socket().remote_endpoint();  
```  
  
Alternatively, you can skip using `beast::tcp_stream` and just use `net::ip::tcp::socket` directly.  Note that both `tcp_stream` and `tcp::socket` use `net::executor` which is a type-erasing wrapper that comes at the cost of a memory allocation.  
  
If you want your acceptor to not use a strand, construct it with the executor returned by `io_context::get_executor`.  
  
I had a look at your code, you will get better results if you pass executors around instead of passing `io_context`.

---

## Comment 8

> Username: chreniuc  
> Created at: 2019-03-19 11:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-474329809  

> I had a look at your code, you will get better results if you pass executors around instead of passing io_context.  
  
Are you referring to this? :  
  
```c++  
socket(::boost::asio::make_strand(ioc))  
// or  
socket = boost::asio::ip::tcp::socket(boost::asio::make_strand(rw_ioc));  
```  
  
to be replaced with:  
  
```c++  
socket(::boost::asio::make_strand(ioc.get_executor()))  
// or  
socket = boost::asio::ip::tcp::socket(boost::asio::make_strand(rw_ioc.get_executor()));  
```  
  
?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-03-19 13:23:42 UTC  
> Updated at: 2019-03-19 13:24:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-474365995  

No. I mean that you should pass executors around internally. For example:  
```  
// rw_ioc is used for write and read operations, save it as a refence to pass it to make_strand  
```  
Instead of storing `rw_ioc`, store `rw_executor`. Add two typedefs:  
```  
using acceptor_executor_type = net::io_context:executor_type;  
using executor_type = net::strand<net::io_context::executor_type>;  
```  
And pass instances of these types in interfaces, rather than passing an I/O context. This won't affect performance, it is just a matter of style. However, it does let you easily experiment with changes. Such as using the `system_context` instead of an `io_context`.

---

## Comment 10

> Username: lowsfer  
> Created at: 2020-12-12 16:39:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-743781545  

Idle ping/disconnect is a nice feature for lost connections.  
But what happens if a malicious client does not respond to a server-sent close frame, but still respond to ping? Is there a timeout for client to respond to close frame?

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-12-12 17:02:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-743784377  

From memory, yes

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-12-12 17:04:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-743784587  

https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream_base__timeout/handshake_timeout.html

---

## Comment 13

> Username: quazeeee  
> Created at: 2021-06-15 18:34:06 UTC  
> Updated at: 2021-06-15 18:39:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-861739196  

This feature can be useful in case of objects destruction. In our situation, we have externa io_conxtext and we can't just stop it. The only possibility is to call async_close and wait for result. Is there any way to close it rapidly in destructor without waiting for async feed back?   
Is there a better solution, then set a timeout (suggested::client) and then just call async_close in the destructor and wait for the onclose handler?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2021-06-15 19:32:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-861775447  

> Is there any way to close it rapidly in destructor without waiting for async feed back?  
  
Yes there is a way to close it rapidly, but not from the destructor. You need to cancel the underlying I/O (e.g. using `asio::socket::cancel`) and wait for the completion handlers to finish. They will complete immediately with `asio::error::operation_aborted`. After all the completion handlers are finished executing, then all calls to `io_context::run` should return (don't forget to also close your listening sockets, if any). Typically `shared_ptr` is used to manage the connection objects.

---

## Comment 15

> Username: quazeeee  
> Created at: 2021-06-15 20:32:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-861811849  

So, looks like there is no solution without waiting for callbacks, in both cases, we need to call some function (async_close or lowest_layey-cancel) and wait for handler (on_close or on_read with abortion). Is it correct?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2021-06-15 22:39:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1122#issuecomment-861881178  

Another way to do it is to just call `io_context::stop()`. This is demonstrated in the example:  
https://github.com/boostorg/beast/blob/710cc53331f197f6f17e8c38454c09df68e43c03/example/websocket/server/chat-multi/main.cpp#L63  
  
"Waiting for callbacks" implies a long duration, but this is not the case. When you cancel I/O (either by calling `cancel` or by closing the socket), all pending operations complete immediately. This is the preferred way to shut down a server, as it gives all the callbacks the opportunity to clean up their resources, if any. For example, you may want to flush data in memory to a database. Using `io_context::stop` makes this more difficult or impossible.
