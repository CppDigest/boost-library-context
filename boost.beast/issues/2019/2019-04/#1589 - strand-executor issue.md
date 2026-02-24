# #1589 - strand/executor issue [Closed]

> Username: Gibson85  
> Created at: 2019-04-23 13:24:22 UTC  
> Updated at: 2021-07-13 02:05:27 UTC  
> Closed at: 2019-04-23 14:06:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1589  

Hi,  
  
my WebSession class looked like this:  
  
```  
class WebSession : public enable_shared_from_this<WebSession>  
{  
public:  
	explicit WebSession(tcp::socket socket) :  
		m_Buffer(),  
		m_WebSocket(std::move(socket)),  
		m_Strand(m_WebSocket.get_executor())  
	{  
	}  
  
private:  
	websocket::stream<tcp::socket> m_WebSocket;  
	boost::asio::strand<boost::asio::io_context::executor_type> m_Strand;  
	boost::beast::multi_buffer m_Buffer;  
  
};  
```  
  
With the latest version boost 1.70 the code doesn't compile anymore at the following line  
  
m_Strand(m_WebSocket.get_executor())  
  
error C2664: 'boost::asio::strand<boost::asio::io_context::executor_type>::strand(boost::asio::strand<boost::asio::io_context::executor_type> &&) noexcept': cannot convert argument 1 from 'boost::asio::executor' to 'const boost::asio::io_context::executor_type &'  
note: Reason: cannot convert from 'boost::asio::executor' to 'const boost::asio::io_context::executor_type'  
note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
  
Any idea how to fix this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-23 13:28:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-485802712  

Yes, remove `m_Strand` and construct the websocket with `boost::asio::make_strand(socket.get_executor())`

---

## Comment 2

> Username: Gibson85  
> Created at: 2019-04-23 13:38:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-485806545  

Thanks for your fast reply. And what about the following calls?  
  
m_WebSocket.async_accept(boost::asio::bind_executor(m_Strand, std::bind(&WebSession::OnAccept, shared_from_this(), std::placeholders::_1)));  
  
m_WebSocket.async_read(m_Buffer, boost::asio::bind_executor(m_Strand, std::bind(&WebSession::OnRead, shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
  
m_WebSocket.async_write(m_Buffer.data(), boost::asio::bind_executor(m_Strand, std::bind(&WebSession::OnWrite, shared_from_this(), std::placeholders::_1, std::placeholders::_2)));

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-23 13:40:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-485807397  

By constructing the websocket with the strand, you do not need to call `bind_executor` anymore, see:  
https://github.com/boostorg/asio/commit/59066d80b26e1d5b83b60d127ee17948d9ae9702  
  
See also:  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p1322r0.html

---

## Comment 4

> Username: Gibson85  
> Created at: 2019-04-23 14:06:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-485818116  

Ok, that solved it. Thanks again.

---

## Comment 5

> Username: ecoindev  
> Created at: 2019-09-10 22:45:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-530149085  

What about `running_in_this_thread()`. How to use this if strand is incapsulated in ws stream object?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-10 22:49:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-530149983  

Use `ws.get_executor().running_in_this_thread()`

---

## Comment 7

> Username: ecoindev  
> Created at: 2019-09-10 23:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-530155988  

`error: no member named 'running_in_this_thread' in 'boost::asio::executor'`  
https://wandbox.org/permlink/ErNto4m8i0RwHF1a

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-09-10 23:50:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-530162897  

You have to use a NextLayer that names the strand instead of the polymorphic executor, something like:  
```  
beast::websocket::stream<  
    asio::ip::tcp::basic_socket<  
        asio::strand<net::io_context::executor_type>>> ws;  
```

---

## Comment 9

> Username: ecoindev  
> Created at: 2019-09-11 08:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-530282571  

Thanks, so in ssl context smth like  
  
`boost::beast::websocket::stream<boost::beast::ssl_stream<  
      boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>,  
                                 boost::beast::unlimited_rate_policy>>>` ?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-09-11 12:52:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-530366247  

Yes just like that

---

## Comment 11

> Username: ecoindev  
> Created at: 2019-09-11 14:36:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-530410306  

Thanks

---

## Comment 12

> Username: ecoindev  
> Created at: 2019-09-29 20:17:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-536337381  

@vinniefalco   
How the example from docs:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/example/websocket/client/async/websocket_client_async.cpp  
will look like in case of applying strand from https://github.com/boostorg/beast/issues/1589#issuecomment-530162897:  
  
https://wandbox.org/permlink/CtsYIKpNIjelFNUw  
  
Will the use of strand executor make possible to concurrently call async_write from several threads, or is a queue necessary?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-09-29 21:44:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-536344347  

> Will the use of strand executor make possible to concurrently call async_write from several threads, or is a queue necessary?  
  
You need a queue. See: https://github.com/boostorg/beast/blob/develop/example/websocket/server/chat-multi/websocket_session.cpp#L102

---

## Comment 14

> Username: ecoindev  
> Created at: 2019-10-01 11:13:15 UTC  
> Updated at: 2019-10-01 11:13:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-536988642  

Thanks, @vinniefalco  
I saw this implementation, but it’s not entirely clear to me how the queue (queue_) is protected from concurrent access from multiple threads.  
  
Right now I am using an atomic write counter with the read-modify-write operation to prevent race condition in call async_write from multiple threads. And as a queue I use boost::asio - in case the writing is already in progress, I just call the boost::post.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-10-01 15:19:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-537087061  

The queue is protected because it is only accessed from the strand:  
https://github.com/boostorg/beast/blob/develop/example/websocket/server/chat-multi/websocket_session.cpp#L90

---

## Comment 16

> Username: ecoindev  
> Created at: 2019-10-01 17:10:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-537134452  

Sorry for the stupid question, but if post to strand protect `this` members from from concurrent access why we need to protect member `ws_` (call async_write) from concurrent execution.  
  
Why can't we just do something like this:  
  
```  
// Can be executed from any thread  
void websocket_session::write(std::string &&ss) {  
  if (!ws.get_executor().running_in_this_thread()) {  
    net::post(ws_.get_executor(),  
              beast::bind_front_handler(&websocket_session::write, shared_from_this(), std::move(ss)));  
  } else {  
    ws_.async_write(net::buffer(ss), beast::bind_front_handler(&websocket_session::on_write, shared_from_this()));  
  }  
}  
```

---

## Comment 17

> Username: vinniefalco  
> Created at: 2019-10-01 17:25:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-537141143  

In your code, `ss` goes out of scope after `ws_.async_write(net::buffer(ss)...` returns and invalidates the buffer, resulting in undefined behavior.

---

## Comment 18

> Username: ecoindev  
> Created at: 2019-10-02 14:03:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-537507349  

> In your code, `ss` goes out of scope after `ws_.async_write(net::buffer(ss)...` returns and invalidates the buffer, resulting in undefined behavior.  
  
Thanks!  
You are absolutely right!!! Perhaps the queue option is not so bad, although it looks a bit clumsy.

---

## Comment 19

> Username: chreniuc  
> Created at: 2019-10-15 19:23:15 UTC  
> Updated at: 2019-10-15 19:43:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-542366087  

@vinniefalco sorry for disturbing you again... We were also using an algorithm with atomics to try to fix the problem with multiple async calls, we've found [your comment](https://github.com/boostorg/beast/issues/1381#issuecomment-449392440) recently and wanted to give it a try.  
  
While trying to implement this I got very confused with the `strand` concept.  
  
Before 1.70.0 we were creating the strands explicitly, just like OP did. Then we updated to 1.70.0 and did the changes to make it work: [here](https://github.com/boostorg/beast/issues/1122#issuecomment-473974440) is a discussion regarding this.  
  
So let's start from the beginning, a strand object is(according to the [official docs](https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/reference/io_context__strand.html)):  
  
> The io_context::strand class provides the ability to post and dispatch handlers with the guarantee that none of those handlers will execute concurrently.  
  
All clear.  
  
Now before 1.70.0 we were creating the strands explicit:  
  
```c++  
ws(std::move(socket(io_context)));  
  
strand(io_context);  
```  
  
And when we wanted to use it, we would pass the strand to the async call:  
  
```c++  
ws.async_accept(  
  boost::asio::bind_executor(  
     strand, std::bind(&WebSession::on_accept, shared_from_this(), std::placeholders::_1)  
  )  
);  
```  
This gave us the freedom to use multiple strands on the same `ws`. For example I could have a `read strand` and a `write strand`, because I do not care if the completion handlers for write and read operations would execute concurrently. I only cared that the read completion handlers will not execute concurrently  with each other and the same thing with the write completion handlers.  
  
Now in `1.7x.0` things have changed, we are creating the `ws` like this:  
  
```c++  
boost::asio::ip::tcp::socket socket( ::boost::asio::make_strand( io_context ) );  
  
boost::beast::tcp_stream stream(::std::move(socket));  
 ::boost::beast::ssl_stream<::boost::beast::tcp_stream> ssl_stream(::std::move(stream));  
  
::boost::beast::websocket::stream<  
    ::boost::beast::ssl_stream<::boost::beast::tcp_stream>>  
    ws(::std::move(ssl_stream));  
```  
  
And calling async methods like this:  
  
```c++  
ws.async_write( boost::asio::buffer( "Message"),  
      std::bind( &ws_server::on_write_websocket, this, message_ptr,  
        std::placeholders::_1,  
        std::placeholders::_2 ) );  
```  
  
All clear, but after reading this issue I got a little bit confused. Notice that I have declared the `ws` as:  
  
```c++  
::boost::beast::websocket::stream<  
    ::boost::beast::ssl_stream<::boost::beast::tcp_stream>>  
    ws(::std::move(ssl_stream));  
```  
  
Not like you said:  
  
>You have to use a NextLayer that names the strand instead of the polymorphic executor, something like:  
  
```c++  
beast::websocket::stream<  
    asio::ip::tcp::basic_socket<  
        asio::strand<net::io_context::executor_type>>> ws;  
```  
  
Does this mean that we are not using strand, even if we did create the socket using `make_strand`?  
  
On which completion handlers does that strand apply(the one we created with `make_strand` and passed to the `socket`)? Read? Write? Both? How can I manually specify which strand to apply on which completion handlers?  
  
Is this a valid case: to use read and write strands? I'm asking this because I see it as a performance improvement to use two strands, the reads and writes are separated and can be done concurrently without any problem...  
  
What is the purpose of `asio::strand<net::io_context::executor_type>` in the type of the `ws`? It's just so we can access more methods? Like `running_in_this_thread`(methods from the strand object)?  
  
LE:  
  
I forgot to mention, we are using `tcp_stream` from beast which is declared like this:  
  
```c++  
using tcp_stream = basic_stream<  
    net::ip::tcp,  
    net::executor,  
    unlimited_rate_policy>;  
```  
  
To apply the strand layer we should do something like this: ?  
  
```c++  
using tcp_stream_strand =  
  basic_stream<net::ip::tcp,  
  boost::asio::strand<boost::asio::io_context::executor_type>,  
  unlimited_rate_policy>  
```  
Thanks :smile:

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-10-15 20:02:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-542380648  

> Is this a valid case: to use read and write strands?   
  
Nope, that will cause undefined behavior, because two different threads could touch the `websocket::stream` object at the same time. Generally speaking, I/O objects **are not thread safe** and may not be accessed concurrently.  
  
The purpose of putting the strand executor on the `stream` is for convenience. It makes calls to initiating functions easier to read (because you don't have to bind the strand executor to the callback every time).

---

## Comment 21

> Username: chreniuc  
> Created at: 2019-10-15 22:02:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-542424054  

Hey @vinniefalco,  
like I said in the previous comment:  
  
> I forgot to mention, we are using tcp_stream from beast which is declared like this..  
  
I did replace `tcp_stream` from beast with:  
  
```c++  
using tcp_stream_strand = ::boost::beast::basic_stream<::boost::asio::ip::tcp,  
  boost::asio::strand<boost::asio::io_context::executor_type>,  
  ::boost::beast::unlimited_rate_policy>;  
```  
  
But I got an error and I do not know how to fix it:  
  
```bash  
/celibs/boost_1_71_0/boost/beast/core/impl/basic_stream.hpp:38:17: error: no matching function for call to 'boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> >::basic_stream_socket(boost::asio::basic_stream_socket<boost::asio::ip::tcp>)'  
  
   38 |     , timer(ex())  
```  
  
Here's the source code, it's only 3 lines in main: https://godbolt.org/z/UwLdY1  
  
```c++  
using tcp_stream_strand = ::boost::beast::basic_stream<::boost::asio::ip::tcp,  
  boost::asio::strand<boost::asio::io_context::executor_type>,  
  ::boost::beast::unlimited_rate_policy>;  
  
int main()  
{  
    boost::asio::io_context ioc;  
    boost::asio::ip::tcp::socket socket(::boost::asio::make_strand(ioc));  
    tcp_stream_strand stream(::std::move(socket)); // here is the error  
   // boost::beast::tcp_stream stream(::std::move(socket)); // This works  
    return 0;  
}  
```  
I do not understand why it doesn't work... What am I doing wrong?

---

## Comment 22

> Username: vinniefalco  
> Created at: 2019-10-15 22:34:15 UTC  
> Updated at: 2019-10-15 22:37:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-542433182  

> I do not understand why it doesn't work... What am I doing wrong?  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/basic_stream.hpp#L240  
  
Should read:  
```  
        net::basic_waitable_timer<  
            chrono::steady_clock,  
                Executor> timer; // rate timer;  
```  
  
I don't think that's your problem though, I'm working on it.

---

## Comment 23

> Username: vinniefalco  
> Created at: 2019-10-15 22:46:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-542436473  

> I do not understand why it doesn't work... What am I doing wrong?  
  
I see the problem. You are attempting to use this constructor overload:  
  
https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/reference/basic_stream_socket/basic_stream_socket/overload10.html  
  
However, this overload is only available if the following condition is met:  
```  
is_convertible< Protocol1, Protocol >::value &&is_convertible< Executor1, Executor >::value  
```  
  
Here, _Executor1_ is `net::executor` while _Executor_ is `net::io_context::executor_type`. You cannot construct a `net::io_context::executor_type` from a `net::executor` (you can go the other way though).  
  
If you want to use the concrete type in the `beast::basic_stream` rather than the polymorphic executor wrapper `net::executor`, then you must also use the concrete type on the socket. This should compile for you:  
  
```  
net::basic_stream_socket<  
    net::ip::tcp,  
    net::strand<  
        net::io_context::executor_type>  
            > sock(net::make_strand(ioc));  
basic_stream<  
    net::ip::tcp,  
    net::strand<  
        net::io_context::executor_type>,  
    unlimited_rate_policy> stream(std::move(sock));  
BOOST_STATIC_ASSERT(  
    std::is_convertible<  
        decltype(sock)::executor_type,  
        decltype(stream)::executor_type>::value);  
```

---

## Comment 24

> Username: chreniuc  
> Created at: 2019-10-17 16:41:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-543260721  

Thanks, that works :smile:   
  
Today I've reread your previous answer again and there are some things that I thought I knew how they work in boost asio...  
  
I though that the purpose of the `strand` is only to avoid having multiple completion handlers invoked concurrently.(serialize completion handlers of the `async` operations)  
  
But the `strand` also serializes the `async` calls. Let me give you an example:  
  
If I'm using a `strand` for a `stream`, when I'm calling two `async` methods from two different threads: `async_read` and `async_write`(on the same `stream`), those calls aren't done in parallel, those calls are serialize. Right?  
  
It's like when you call `async_read`, this will be posted in the internal `strand` of the `stream`, same thing for the `async_write`. Then the worker thread will get the `async_read` from the `strand` and execute it(which should return immediately) and then it calls `async_write`(I'm not talking about completion handler, those will also be posted after the async calls are made).  
  
Otherwise there would be two calls on the `stream` object from two different threads, and there would be a data race. Because:  
  
> I/O objects are not thread safe and may not be accessed concurrently.  
  
Can you confirm this? Or the async calls do not modify the `stream` so there is no need to serialize them?  
  
Thank you again, really. You've helped me alot. :smile:

---

## Comment 25

> Username: vinniefalco  
> Created at: 2019-10-17 16:44:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-543262168  

> If I'm using a strand for a stream, when I'm calling two async methods from two different threads: async_read and async_write(on the same stream), those calls aren't done in parallel, those calls are serialize. Right?  
  
This is undefined behavior. You **cannot call any member functions concurrently**. You must make sure that only one thread is accessing the socket at a time. The easiest way to do this is to use a strand. See:  
![image](https://user-images.githubusercontent.com/1503976/67029736-b99e6d00-f0c2-11e9-8751-b20c96ac1adb.png)

---

## Comment 26

> Username: chreniuc  
> Created at: 2019-10-17 16:49:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-543264625  

Perfect. Thanks. This is something that I didn't knew :smile:

---

## Comment 27

> Username: uvguy  
> Created at: 2021-07-13 01:16:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-878702363  

@vinniefalco , apologize for spamming stupid question here, I have dummy broadcast ws server here, but my first intention is when the server successfully broadcast write data then the server must read client request again. How to avoid soft_mutex try_lock assertion when [async_read](https://gist.github.com/uvguy/0355caabdfc78f5233cb7df79a8a6a03#file-main-cpp-L167) on my gist  .. thanks

---

## Comment 28

> Username: vinniefalco  
> Created at: 2021-07-13 02:05:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1589#issuecomment-878720737  

Please open a new issue, thanks !
