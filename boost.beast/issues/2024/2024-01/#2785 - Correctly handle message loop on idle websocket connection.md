# #2785 - [Help] Correctly handle message loop on idle websocket connection [Closed]

> Username: zptan  
> Created at: 2024-01-03 09:27:00 UTC  
> Updated at: 2024-01-13 02:03:22 UTC  
> Closed at: 2024-01-13 02:03:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2785  

We have a few websocket connections that may not be always busy, sometimes they just ping-pong (automatically handled) with server if no request sent, so `io_context.run()` exits and `io_context.stopped()` returns true at that time (_**please correct me if I am wrong**_).  
  
So what is the correct or elegant way to handle message loop?  
  
Always restart `io_context` if it is stopped?  
  
```cc  
// Run in a separated thread  
while (!shutdown) {  
    while (!io_context_.stopped()) {  
        io_context_.run();  
    }  
    io_context_.restart();  
}

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-03 09:32:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1875075193  

[websocket::stream](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html) handles ping-pong as long as you have an active async_read operation. so you need to always have an async read loop in your design.

---

## Comment 2

> Username: zptan  
> Created at: 2024-01-03 09:47:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1875094728  

@ashtum Thx.  
  
Typical websocket client subscribes a topic and keep receiving data from server.  
  
But my websocket client behaves like HTTP, sends request then receives response, it is one-shot, then send and receive...  
  
So if there is no request sent, ping-pong will not be handled automatically? So I have to call `control_callback()` to handle ping-pong explicitly?  
  
Will the callback be called by `io_context.run()`?  
  
```cc  
ws_.control_callback([](frame_type kind, string_view payload) {  
    if (kind == ping) {  
        async_pong(...);  
    }  
});

---

## Comment 3

> Username: ashtum  
> Created at: 2024-01-03 10:01:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1875111557  

Based on your use case, you will always have an ongoing `async_write` or `async_read`. Therefore, there is no need to manually handle ping-pong operations; instead, you just need to set the proper timeout options using [websocket::stream::set_option (2 of 3 overloads)](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option/overload2.html).  
  
As long as you have an unfinished async operation, `io_context.run()` should not return. [io_context::run (1 of 2 overloads)](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/io_context/run/overload1.html).

---

## Comment 4

> Username: zptan  
> Created at: 2024-01-03 12:07:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1875272624  

> Based on your use case, you will always have an ongoing async_write or async_read.  
  
We cannot assume how/when caller calls the client, i.e., The first request may be sent (async_write) 1h after connection created, so `io_context.run()` exits after connection created. The request will not be sent since `io_context` has stopped.  
  
I can keep calling `io_context.restart()` if `io_context` is stopped, but it wastes CPU resource.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-01-03 12:59:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1875335544  

Is this part of a library interface and you hide the `io_context` from the user?  
You can create an [executor_work_guard](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/executor_work_guard.html) to prevent `io_context` from stop but in a well designed async application you rarely need to use this (because there is always something to wait for).

---

## Comment 6

> Username: zptan  
> Created at: 2024-01-03 13:04:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1875344045  

> Is this part of a library interface and you hide the io_context from the user?  
  
Yes, user does not know io_context.  
  
Thx, I will take a look.

---

## Comment 7

> Username: zptan  
> Created at: 2024-01-04 11:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1876916850  

> We cannot assume how/when caller calls the client, i.e., The first request may be sent (async_write) 1h after connection created, so `io_context.run()` exits after connection created. The request will not be sent since `io_context` has stopped.  
>   
> I can keep calling `io_context.restart()` if `io_context` is stopped, but it wastes CPU resource.  
  
I tried [executor_work_guard](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/executor_work_guard.html) but it does not work.  
  
However I managed to keep running `io_context.run()` without calling `io_context.restart()` by scheduling a task to ping the server periodically by using `steady_timer`, and this can be used to check the connectivity.  
  
So we have 2 pipelines:  
1. Pings the server periodically by `io_context.run()`, the server responds with payload  
2. User may call `boost::asio::dispatch()` to send request and receive response from a separated thread, it is a one-shot operation.  
  
So my concern is:  
Is it possible that pipeline 2 will receive payload of pipeline 1? vice versa.  
  
I know 2 pipelines both call `async_write()` which will put a handler (async_read) to io_context, `io_context.run()` calls these handlers, but I am not sure how the handler be called, if handler A (async_read) was put before handler B (async_read), is A guaranteed to be called before B?  
  
As code shows below  
```cc  
// thread that runs `io_context.run()`  
async_write(payloadA, [](...) {  
    handlerA(...);  
});  
  
std::thread separated_thread{  
...  
    async_write(payloadB, [](...) {  
        handlerB(...);  
    });  
}  
```  
  
I guess that we can not assume the order how handler called since TCP does not guarantee packet order in a sending/receiving window, so a packet sent later may get response earlier, a packet sent earlier may get response later because of network congestion.  
  
But I need to confirm.

---

## Comment 8

> Username: ashtum  
> Created at: 2024-01-04 11:28:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1876939947  

You cannot have more than one ongoing `async_read` operation; you have to wait until it completes to initiate another one. This is true for `async_write` as well.  
  
> I tried [executor_work_guard](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/executor_work_guard.html) but it does not work.  
  
Here is a simple usage example: https://godbolt.org/z/W58hjjvza  
  
```C++  
#include <boost/asio.hpp>  
  
using namespace boost;  
  
int main()  
{  
  asio::io_context ioc;  
  
  auto wg = asio::make_work_guard(ioc);  
  
  ioc.run();  
}  
```  
  
An alternative for you might be to run `async_read` in a loop and send the received messages to an [experimental::basic_channel](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/experimental__basic_channel.html). This way, `async_read` takes care of ping-pong operations. After your write operation, you can wait on the channel to read the response.  
  
If this doesn't work for you, please explain your problem more thoroughly and provide example code of your interfaces so that I can better understand what you want to achieve.

---

## Comment 9

> Username: zptan  
> Created at: 2024-01-04 12:32:33 UTC  
> Updated at: 2024-01-04 13:48:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1877024460  

@ashtum Thanks for you reply.  
  
> You cannot have more than one ongoing async_read operation; you have to wait until it completes to initiate another one. This is true for async_write as well.  
  
Oh sorry, the code I pasted in my last reply is a little confusing, actually like this (using `dispatch()`)  
```cc  
// thread that runs `io_context.run()`  
async_write(payloadA, [](...) {  
    handlerA(...);  
});  
  
std::thread separated_thread{  
...  
    // Thread safe  
    boost::asio::dispath(ws->get_executor(), [](...){  
        async_write(payloadB, [](...) {  
            handlerB(...);  
       });  
   });  
}  
```  
  
About [executor_work_guard](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/executor_work_guard.html), I have a question in the comment  
  
Update: It works, previously I followed the [doc](https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/io_context.html#boost_asio.reference.io_context.stopping_the_io_context_from_running_out_of_work), I don't know why it does not work, however your code helps a lot.  
```cc  
int main()  
{  
  asio::io_context ioc;  
  auto wg = asio::make_work_guard(ioc);  
  std::thread runner{[&ioc] {  
    ioc.run();  
  }};  
  
  ...  
  // A long time passed, ioc has no handler to run  
  
  async_write(payload, []() {  
    Handler(...);      // <------------- This handler be run by ioc still!  
  });  
  
  ...  
}  
```  
  
> An alternative for you might be to run async_read in a loop and send the received messages to an [experimental::basic_channel](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/experimental__basic_channel.html). This way, async_read takes care of ping-pong operations. After your write operation, you can wait on the channel to read the response.  
  
I guess we are not on the same page, my app is the client  
```  
 Client                          Server  
   |  ------ order request ----->  |  
   |  <---- order response ------  |  
   |             ...               |  
   |  <--------  ping  ----------  |  
   |  ---------  pong  --------->  |  
   |             ...               |  
   |  ------ order request ----->  |  
   |  <---- order response ------  |  
   |             ...               |  
   |  ---------  ping  --------->  |  
   |  <--------  pong  ----------  |  
```  
So it is not a typical websocket scenario.  
  
We can not assume the order request frequency, the client may send only a few order requests in a hour, and may send 100 order requests in a minute.  
  
So to prevent io_context from running out of handlers, I use a `steady_timer` to ping the server periodically. But I'm not sure:  
1. Will order response handler receive a pong message?  
2. Will ping response handler receive a order response?

---

## Comment 10

> Username: ashtum  
> Created at: 2024-01-04 14:28:14 UTC  
> Updated at: 2024-01-04 14:28:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1877185238  

> Oh sorry, the code I pasted in my last reply is a little confusing, actually like this (using dispatch())  
  
The problem is not just about data race. You can't have more than one `async_read` even from a single thread because each one might need multiple calls to `async_read_some` to gather all the required data from the socket. When you have more than one `async_read`, each part of the data can interleave with each other. [async_read (1 of 8 overloads)](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/async_read/overload1.html).  
  
> So it is not a typical websocket scenario.  
  
Could you please explain what would be the interface of your library?  
  
To my understanding, your concern about ping-pong operations would be addressed by always having an async_read operation waiting. It takes care of the ping-pong, and there is no need to handle it manually.  
  
If there is a guarantee of having a single response for each request, you can send responses in your async_read loop to an [experimental::basic_channel](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/experimental__basic_channel.html) and read from it after the async_write completes.

---

## Comment 11

> Username: zptan  
> Created at: 2024-01-04 16:06:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1877354733  

Thanks.  
  
> Could you please explain what would be the interface of your library?  
  
My lib provides a websocket client to operate orders by communicating with the server.  
  
The most important method is `SendRequest()` which sends order request and handles order response  
```cc  
class WsSession : public enable_shared_from_this<WsSession> {  
public:  
  ...  
  
  void SendRequest(std::shared_ptr<WsConnection> ws_conn) {  
    boost::asio::dispatch(ws_conn->GetExecutor(), [this, ws_conn, session = shared_from_this()] {  
        ws_conn->Write(  
            std::move(payload_),  
            [ws_conn, session](const error_code write_ec, const std::size_t /* nbytes */) {  
                if (write_ec) {  
                    // cook error message  
                    session->OnFail(std::move(error));  
                    return;  
                }  
  
                ws_conn->Read([session](const error_code read_ec, buffer& buf) {  
                    // Read and process order response from buffer  
                    session->OnRead(read_ec, buf);  
                });  
            });  
    });  
  }  
  ...  
};  
```  
  
> To my understanding, your concern about ping-pong operations would be addressed by always having an async_read operation waiting. It takes care of the ping-pong, and there is no need to handle it manually.  
  
But the connection may be disconnected (the server will kill those connections that >24h, or poor network condition), so my lib `WsConnection` has to proactively check the connectivity, e.g., send a test request (e.g., ping) per 1min, then `async_read()` the response, and check the error_code, reconnect if the error_code is `websocket::error::closed`.  
  
Oh I just found [async_ping](https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_ping.html#beast.ref.boost__beast__websocket__stream.async_ping.description)  
> The algorithm, known as a composed asynchronous operation, is implemented in terms of calls to the next layer's async_write_some function. The program must ensure that no other calls to [ping](https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/ping/overload1.html), [pong](https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/pong/overload1.html), [async_ping](https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_ping.html), or [async_pong](https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_pong.html) are performed until this operation completes.  
  
> If a close frame is sent or received before the ping frame is sent, the error received by this completion handler will be net::error::operation_aborted.   
  
  
So can I use `async_ping` to check the connectivity?  
  
Will it causes issue since the server also pings clients and my client will pong the server automatically by default?

---

## Comment 12

> Username: ashtum  
> Created at: 2024-01-04 16:14:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1877368308  

`async_read` can handle sending ping messages and check for the pong, you need to use [websocket::stream::set_option (2 of 3 overloads)](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option/overload2.html) for specifying the required timings.  
  
I recommend reading the documentation and examples first before attempting to design your library.

---

## Comment 13

> Username: zptan  
> Created at: 2024-01-04 16:21:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1877383238  

Thanks, I will try.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2024-01-12 17:48:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1889716974  

`executor_work_guard` is the correct solution. Please show the code using the guard.

---

## Comment 15

> Username: zptan  
> Created at: 2024-01-13 01:56:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1890248074  

> `executor_work_guard` is the correct solution. Please show the code using the guard.  
  
@vinniefalco Thx, previously I followed the [boost doc](https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/io_context.html#boost_asio.reference.io_context.stopping_the_io_context_from_running_out_of_work) and it didn't work(there might be a bug in my code), after that I tried [the code from @ashtum](https://github.com/boostorg/beast/issues/2785#issuecomment-1876939947), and it did work, thx @ashtum

---

## Comment 16

> Username: zptan  
> Created at: 2024-01-13 02:03:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2785#issuecomment-1890250173  

And I found the solution from these issues  
- https://github.com/boostorg/beast/issues/2153  
- https://github.com/boostorg/beast/issues/1381  
  
If requests may be sent from multiple threads by one connection, we need a strand, and a queue, and requests must be processed one by one, a request should not be sent until the previous request completed (response received).
