# #2747 - How actually destroy websocket object ? [Closed]

> Username: raidenluikang  
> Created at: 2023-10-08 10:59:29 UTC  
> Updated at: 2023-11-01 11:47:48 UTC  
> Closed at: 2023-11-01 11:47:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2747  

### Version of Beast  
1.78.0  
  
### Steps necessary to reproduce the problem  
  
created boost/websocket: `boost::beast::websocket::stream< boost::beast::ssl_stream< boost::beast::tcp_stream > >` object,  worked  in separate working thread with boost::asio::io_context::run.  
  
Receive stop command from the users in some other thread. I stop boost::asio::io_context, join working thread. And deleted the websocket object. Manually don't call the close method.  
  
Tests sometimes crashed with the following assertion.  
  
What's wrong ?  
  
```  
boost_1_78_0/boost/beast/websocket/detail/soft_mutex.hpp:75: void boost::beast::websocket::detail::soft_mutex::unlock(const T*) [with T = boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> > >::close_op<std::_Bind<void (::websocket_client::*(std::shared_ptr<::websocket_client>, std::_Placeholder<1>))(const boost::system::error_code&)> >]: Assertion `id_ == T::id' failed.  
terminate called without an active exception  
Aborted (core dumped)  
  
```  
### All relevant compiler information  
  
GCC 9.4   Ubuntu 20.04

---

## Comment 1

> Username: fpelliccioni  
> Created at: 2023-10-08 11:41:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1752006319  

Thank you for reporting this issue with detailed information.  
  
From the description, it sounds like there might be some sort of race condition or misuse related to the destruction of the websocket object and the associated `io_context`. It's essential to ensure proper synchronization and orderly shutdown of asynchronous operations and the `io_context` itself to prevent undefined behavior and crashes.  
  
Before we proceed further with the investigation, could you provide more details on how you are managing the lifecycle of your `io_context` and websocket objects? Particularly, are there any outstanding asynchronous operations on the websocket when you attempt to destroy it? Understanding the sequence of operations leading to the object's destruction will help in diagnosing the issue.  
  
Additionally, could you possibly provide a minimal, complete, and verifiable example that reproduces the issue? Having a small code sample that we can run and explore directly would be immensely helpful in pinpointing the problem more accurately and efficiently.

---

## Comment 2

> Username: raidenluikang  
> Created at: 2023-10-11 09:38:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1757270594  

Hey , I found my mistake.   
websocket object calls  async_close, not waiting end of async_handshake.  
  
async_close:  
```  
socket.async_close(boost::beast::websocket::close_code::normal,  
                 std::bind( &websocket_client::on_close, self, std::placeholders::_1)  
             );  
```  
  
And async_handshake code.  
  
```  
 socket.next_layer().async_handshake(boost::asio::ssl::stream_base::client,  
                std::bind(&websocket_client::on_ssl_handshake, self, std::placeholders::_1)   
            );  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-10-11 13:15:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1757680159  

Ah yes. The handshake must complete before any other websocket operations may be performed.

---

## Comment 4

> Username: raidenluikang  
> Created at: 2023-10-12 09:16:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1759237998  

However, Why parallel async operation cause an Assertion?  Couldn't be second async operation complete with error code ?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-10-12 11:03:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1759394715  

> Couldn't be second async operation complete with error code ?  
  
No, because the functions have preconditions.

---

## Comment 6

> Username: raidenluikang  
> Created at: 2023-10-12 12:15:13 UTC  
> Updated at: 2023-10-12 12:15:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1759489494  

How to close websocket , if  `async_hanshake`  or other async operation doesn't complete during a long time (or specific time, 10 seconds, ex.) ?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-10-12 12:55:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1759560623  

if `async_handshake` fails to complete, there is no "websocket". Just close the socket like normal (use `next_layer()` to access that).

---

## Comment 8

> Username: raidenluikang  
> Created at: 2023-10-12 13:34:06 UTC  
> Updated at: 2023-10-12 13:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1759625439  

No, there `async_handshake`  still working after 10 seconds, And I decided finish it with timeout, how stop it after 10 seconds ?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2023-10-12 21:33:26 UTC  
> Updated at: 2023-10-12 21:33:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1760393497  

Set an asio timer, and if the timer expires close the socket.

---

## Comment 10

> Username: raidenluikang  
> Created at: 2023-10-13 10:29:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1761287449  

It's initial problem,  I already set the boost::asio::steady_timer,  after timeout , websocket closed  with async_close , and result is Assertion :)  
  
How to complete async_handshake  itself with timeout ?

---

## Comment 11

> Username: klemens-morgenstern  
> Created at: 2023-10-13 10:42:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1761303109  

Either `cancel` on the websocket, a [cancellation signal](https://www.boost.org/doc/libs/1_83_0/doc/html/boost_asio/overview/core/cancellation.html) or closing the underlying socket should do.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2023-10-17 14:33:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1766546796  

> Either cancel on the websocket, a cancellation   
  
No, that's not a good idea. Closing the socket is the only correct behavior in response to a timeout during the handshake. @raidenluikang we are talking about a socket closure, not a websocket close. That means you call `websocket::stream::next_layer().close()` not `websocket::stream::async_close()`.  
  
When a handshake times out, there is no way to know the state of the connection, and therefore the socket must be closed. Canceling leaves the socket in the inconsistent state, where the only valid operation is closure. So skip the cancel, and just close.

---

## Comment 13

> Username: fpelliccioni  
> Created at: 2023-10-23 15:28:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1775462222  

@raidenluikang  Were you able to get your code working based on Vinnie's suggestions?

---

## Comment 14

> Username: raidenluikang  
> Created at: 2023-11-01 11:46:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2747#issuecomment-1788821365  

@fpelliccioni Yes, I able to apply that suggestions. Thanks.  Issue may be closed).
