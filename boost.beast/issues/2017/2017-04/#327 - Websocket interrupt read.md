# #327 - Websocket interrupt read [Closed]

> Username: markvasiv  
> Created at: 2017-04-27 17:12:23 UTC  
> Updated at: 2017-04-27 20:58:07 UTC  
> Closed at: 2017-04-27 20:58:07 UTC  
> Url: https://github.com/boostorg/beast/issues/327  

I'm implementing a WebSocket client using the beast library and faced a problem with sync read operation. When I want to destroy a connection I use `WebSocket.close()` function. I was expecting this call to immediately interrupt the read operation, however, `read()` call can hang for a minute before it actually returns. Is there a way to immediately interrupt any ongoing read operations?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-27 20:18:12 UTC  
> Url: https://github.com/boostorg/beast/issues/327#issuecomment-297827406  

There are two problems here. First, note that the only way you could possibly be calling `close` while a call to `read` is active is from another thread. `boost::asio::ip::tcp::socket` objects are not thread-safe, either using the synchronous or the asynchronous APIs.  
  
Second, there is no way to interrupt a synchronous read. If you need this functionality, you must use asynchronous interfaces. This is the interface of Asio, and since Beast builds on top of Asio it has the same limitations.

---

## Comment 2

> Username: markvasiv  
> Created at: 2017-04-27 20:36:09 UTC  
> Url: https://github.com/boostorg/beast/issues/327#issuecomment-297831915  

Ok, I see. Do I understand correctly that even if I switch to the async interface, I cannot call, for example, `async_read()` and `async_close()` from different threads?  
  
Is there a way to quickly close WebSocket connection if I switch to the async interface? Imagine I have an object which holds references to all the WebSocket-related objects, and at some point, I want to destroy that object as quickly as possible. So is that possible to do that?  
  
Another question is how WebSocket behaves under unreliable network conditions. What is the common pattern to react to a network loss and appearance while maintaining an active connection?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-27 20:47:07 UTC  
> Updated at: 2017-04-27 20:47:57 UTC  
> Url: https://github.com/boostorg/beast/issues/327#issuecomment-297834676  

>Do I understand correctly that even if I switch to the async interface, I cannot call, for example,  
>async_read() and async_close() from different threads?  
  
Yes, that is correct. Note that this limitation applies not only to `beast::websocket::stream` objects but also `boost::asio::ip::tcp::socket` objects (and anything that contains them). See "Thread Safety" here:  
http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/reference/ip__tcp/socket.html  
  
>Is there a way to quickly close WebSocket connection if I switch to the async interface?  
  
Yes. Given this declaration:  
```  
beast::websocket::stream<boost::asio::ip::tcp::socket> ws{...};  
```  
  
You can:  
* Cancel all pending I/O using `ws.next_layer().cancel()`  
* Close the underlying socket using `ws.next_layer().close()`  
  
In either case, any pending asynchronous operations will complete immediate with `boost::asio::error::operation_aborted`. If you destroy the stream object right after calling `cancel` or `close`, note that the completion handlers may be called after the stream object is destroyed. You can check if the error is `operation_aborted` at the very beginning of your handler and just return from the function instead of accessing members of the stream (since the stream could be destroyed).  
  
The functions `cancel` and `close` are not thread safe. They must be called from the same implicit or explicit strand as the one used to perform the asynchronous operations. An easy way to do that is to call `io_service::post` if you're not already on the strand's context. For example:  
```  
void T::do_close()  
{  
    if(! strand_.running_in_this_thread())  
        return strand_.post(std::bind(&T::do_close, shared_from_this()));  
    socket_.close();  
```  
  
See also:  
http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/reference/basic_stream_socket/cancel.html  
http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/reference/basic_stream_socket/close.html  
  
>Imagine I have an object which holds references to all the WebSocket-related objects, and at  
> some point, I want to destroy that object as quickly as possible. So is that possible to do that?  
  
Yes it is possible, but I have to warn you that this is actually a very difficult problem to solve correctly! You are describing a container that holds references to objects that are executing asynchronously. And those objects can be created and destroyed from other threads. How would you iterate such a container? Some kind of lock is needed. If you're using shared pointers, you have to be careful because if the container holds a strong reference to your object then it can never be destroyed! How will you know when to destroy the objects? It gets very complicated very quickly. This is not specific to Beast but is a general problem when building programs using Asio's asynchronous model.  
  
>Another question is how WebSocket behaves under unreliable network conditions. What is the   
>common pattern to react to a network loss and appearance while maintaining an active connection?  
  
Beast.WebSocket doesn't do anything special. Its up to you to detect the loss of connection, by checking the error codes from your asynchronous operations and taking action. Beast doesn't manage the connection for you, it has no idea about IP addresses or listening sockets. It just wraps your already-existing `socket` or `ssl_stream` (or user-defined type).  
  
Thanks for your interest in Beast!

---

## Comment 4

> Username: markvasiv  
> Created at: 2017-04-27 20:54:26 UTC  
> Url: https://github.com/boostorg/beast/issues/327#issuecomment-297836464  

Thank you for the great library and such quick and comprehensive answers!   
May I leave this issue open in case I have any specific problems while re-implementing the client with the async approach or it is better to open a new one then?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-04-27 20:57:25 UTC  
> Url: https://github.com/boostorg/beast/issues/327#issuecomment-297837252  

>May I leave this issue open in case I have any specific problems while re-implementing the client with  
>the async approach or it is better to open a new one then?  
  
Whatever works better for you! Probably a separate issue is best, in case anyone tries to search later.

---

## Comment 6

> Username: markvasiv  
> Created at: 2017-04-27 20:58:07 UTC  
> Url: https://github.com/boostorg/beast/issues/327#issuecomment-297837419  

Ok, closing then.
