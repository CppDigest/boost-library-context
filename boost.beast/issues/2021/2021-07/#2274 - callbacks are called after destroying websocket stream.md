# #2274 - callbacks are called after destroying websocket stream [Closed]

> Username: quazeeee  
> Created at: 2021-07-01 17:48:00 UTC  
> Updated at: 2022-09-24 05:12:28 UTC  
> Closed at: 2022-09-24 05:12:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2274  

Hello. Maybe someone can provide advice on how to handle pending handlers due to the websocket close event. Thank you in advance.  
Precondition:  
1) For callbacks, I don't use shared_pts, it is just 'this'.   
async_ready(buffer, bind_front_handler(*MySocketWrapperSocket::OnRead, this)  
2) I want to avoid using io_service.stop(), but suggestions with such calls also would be helpful.   
  
not working solution:  
I tried to do such thing in destructor:  
~MySocketWrapperSocket() {  
std::future<void> f = stream->async_close(code, asio::use_future);  
f.wait();  
}  
but in this case the OnRead handler will be called after object destruction, which, from my understanding not safe, because 'this' object already destroyed.   
Is it correct? How can we close the socket and be insured that callbacks will not be called for destroyed class?

---

## Comment 1

> Username: quazeeee  
> Created at: 2021-07-01 18:47:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872471400  

if websocket_stream uses strand for execution, can we somehow handle operation_abortion in on_read before on_close event?

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-07-01 18:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872476193  

waiting on the future is preventing progress of the io context.  
  
Simply invoke async_close with an empty lambda. The currently running async_read will complete with an error (websocket::error::closed) and any pending write will either complete with no error or will complete with operation_aborted.  
  
The only place you need to detect the closure is in the handler for the async_read.  
  
```  
stream->async_close(code, [](error_code const&) { /* nothing needed here */ });  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-07-01 20:39:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872535868  

You're gonna have to have some form of reference counting to know which completion handler is "last." Otherwise, how will you know when to destroy the socket?

---

## Comment 4

> Username: reddwarf69  
> Created at: 2021-07-01 20:40:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872536419  

> can we somehow handle operation_abortion in on_read before on_close event?  
  
No.  
  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html  
  
> Send a websocket close control frame asynchronously.  
  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html  
  
> A close frame is received. In this case the error indicated by the function will be error::closed.   
  
When you send the close frame the other side is likely to say "if he doesn't want to send me anything else I don't want to send him anything else either, I will also send him a close control frame".  
  
Your async_close completion handler returns when you send the close frame  
...  
The packet arrives to the other side  
...  
The other side send you a close frame  
...  
You reveive the close frame and async_read completes.  
  
You can't make the close frame reply arrive before you send your close frame. It's simply phisically impossible.  
  
  
> I tried to do such thing in destructor:  
  
Nothing you try to do there is ever going to work.  
https://youtu.be/D-lTwGJRx0o?t=2000  
  
  
> I don't use shared_pts  
  
Bad idea.  
You need to ensure the stream is not destructed until all the asynchronous operations complete. That's it, it's your job. Since you are going to have multiple asynchronous operations you are going to need reference counting. shared_ptr does the reference counting for you. If you don't want to use it it's your decision, but now you are going to have to implement that reference counting yourself.

---

## Comment 5

> Username: quazeeee  
> Created at: 2021-07-01 20:42:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872537208  

> waiting on the future is preventing progress of the io context.  
>   
> Simply invoke async_close with an empty lambda. The currently running async_read will complete with an error (websocket::error::closed) and any pending write will either complete with no error or will complete with operation_aborted.  
>   
> The only place you need to detect the closure is in the handler for the async_read.  
>   
> ```  
> stream->async_close(code, [](error_code const&) { /* nothing needed here */ });  
> ```  
  
it will not solve the problem, because we still can have on_read completion handler which will hold destroyed 'this' object.

---

## Comment 6

> Username: quazeeee  
> Created at: 2021-07-01 20:51:13 UTC  
> Updated at: 2021-07-01 20:52:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872541932  

> You're gonna have to have some form of reference counting to know which completion handler is "last." Otherwise, how will you know when to destroy the socket?  
  
It is one of my current ideas too. But this solution quite messy, in general, it will require some reference over all code to make increments before async operation call and decriment in the completion handler. Also, we will need additional flag to decide should we call another on_read or not. How I see it (other handlers will be handled similar, e.g. on_write):  
```  
  
on_connect() {  
..........  
ref_count++;  
async_read(&on_read, this);  
}  
  
  
on_read(...) {  
if(ec) {  
fail(ec)  
}  
......  
on_read(&on_read, this);  
}  
`  
fail(ec) {  
........  
ref--;  
}  
  
~() {  
while(ref) {  
sleep();  
}  
}  
  
```

---

## Comment 7

> Username: quazeeee  
> Created at: 2021-07-01 21:00:16 UTC  
> Updated at: 2021-07-01 21:00:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872546338  

> > can we somehow handle operation_abortion in on_read before on_close event?  
>   
> No.  
>   
> https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html  
>   
> > Send a websocket close control frame asynchronously.  
>   
> https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html  
>   
> > A close frame is received. In this case the error indicated by the function will be error::closed.  
>   
> When you send the close frame the other side is likely to say "if he doesn't want to send me anything else I don't want to send him anything else either, I will also send him a close control frame".  
>   
> Your async_close completion handler returns when you send the close frame  
> ...  
> The packet arrives to the other side  
> ...  
> The other side send you a close frame  
> ...  
> You reveive the close frame and async_read completes.  
>   
> You can't make the close frame reply arrive before you send your close frame. It's simply phisically impossible.  
>   
> > I tried to do such thing in destructor:  
>   
> Nothing you try to do there is ever going to work.  
> https://youtu.be/D-lTwGJRx0o?t=2000  
>   
> > I don't use shared_pts  
>   
> Bad idea.  
> You need to ensure the stream is not destructed until all the asynchronous operations complete. That's it, it's your job. Since you are going to have multiple asynchronous operations you are going to need reference counting. shared_ptr does the reference counting for you. If you don't want to use it it's your decision, but now you are going to have to implement that reference counting yourself.  
  
shared_ptr has one nasty drawback. Imagine that You use `std::shared_ptr<WebSocketWrapper>(); `  
For some reason, you stack unwinding now and the destructor of WebSocketWrapper will not be called, because the completion handler holds one instance of shared_ptr, the connection will be alive, while program actually not.   
I see only two ways for make it properly right now:  
1) use io_service inside the class and shut down it in dctor  
2) counting async_handlers

---

## Comment 8

> Username: reddwarf69  
> Created at: 2021-07-01 21:09:12 UTC  
> Updated at: 2021-07-01 21:10:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872551545  

```  
~() {  
while(ref) {  
sleep();  
}  
}  
```  
If the thing calling the destructor is the same thing calling io_context.run() then the completion handlers will never run, ref-- will never be executed and you will stay in that loop forever.  
  
Plus the object lifetime ends when the destructor begins, even if the completion handlers were to run `this` would be pointing to an object that's not alive any more and you would be invoking undefined behaviour. If you are in the destructor it's already too late.  
  
  
> shared_ptr has one nasty drawback.  
  
shared_ptr has been used by the author of ASIO since before C++11.  
  
https://www.boost.org/doc/libs/1_76_0/doc/html/boost_asio/reference/io_context/_io_context.html  
  
> The destruction sequence described above permits programs to simplify their resource management by using shared_ptr<>  
  
  
> the destructor of WebSocketWrapper will not be called, because the completion handler holds one instance of shared_ptr, the connection will be alive, while program actually not.  
  
Either you keep running until all those completion handlers complete, or you end up destroying the io_context and:  
  
https://www.boost.org/doc/libs/1_76_0/doc/html/boost_asio/reference/io_context/_io_context.html  
  
> Uninvoked handler objects that were scheduled for deferred invocation on the io_context, or any associated strand, are destroyed.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-07-01 21:11:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872552691  

> ...this solution quite messy  
  
Not really. You use `shared_ptr<C>` where `C` is the object which owns the socket. Then you bind a copy of the `shared_ptr` into every completion handler. This is how the examples work:  
  
https://github.com/boostorg/beast/blob/710cc53331f197f6f17e8c38454c09df68e43c03/example/websocket/server/chat-multi/websocket_session.cpp#L57  
  
There is nothing "messy" about this.

---

## Comment 10

> Username: quazeeee  
> Created at: 2021-07-01 22:37:04 UTC  
> Updated at: 2021-07-01 22:38:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872591375  

All ways lead to shared_ptr. Ahh. Can we expect anything else except `operation_abortion` after  
 `on_close`   
or   
```  
std::future f = stream->async_close(code, asio::use_future);  
f.wait();  
```  
completion? I am wondering about handling events after closing the socket. Because usually, we want to make reconnect in on_fail callback.

---

## Comment 11

> Username: quazeeee  
> Created at: 2021-07-01 23:04:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872600931  

With one strand executor for all handlers, it should be only operation_abortion after on_close event from my understanding.

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-07-02 05:24:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872724931  

Reconnect on fail is best done by implicitly dropping the connection object and creating a new shared connection object. The implicit drop will happen when the last handler (containing the shared_ptr copy) is invoked and then destroyed without initiating another async operation.

---

## Comment 13

> Username: reddwarf69  
> Created at: 2021-07-02 05:52:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872736172  

> after closing the socket  
  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html doesn't "close the socket"; it "Send a websocket close control frame", it completes when "The close frame finishes sending".  
It's the equivalent of doing shutdown(s, SHUT_WR) in a TCP socket: it's going to send a FIN packet, but recv() can keep reading data forever, only when/if receives another FIN packet will recv() return 0. shutdown() which by the way is exactly what https://datatracker.ietf.org/doc/html/rfc6455#section-7.1.1 shows and https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/teardown.hpp#L116 does.  
  
async_read can keep receiving data forever after async_close. Only when "A close frame is received" (which is likely, but not guaranteed to ever happen) async_read will return and "In this case the error indicated by the function will be error::closed", not operation_aborted.  
Only if you use https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream_base__timeout/handshake_timeout.html you have any guarantee of async_read() ever returning, and it would return timeout (https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__error.html).  
  
You would need to use https://www.boost.org/doc/libs/1_76_0/doc/html/boost_asio/reference/basic_stream_socket/close.html to actually "close the socket", which would make async_read() return operation_aborted. And even in this case there is no guarantee you will not receive some data before operation_aborted if the data had already arrived and the completion handler was already scheduled for execution before you closed the socket.  
  
So yeah, follow madmongo1 advice and "Reconnect on fail is best done by implicitly dropping the connection object and creating a new shared connection object." (i.e. use another websocket::stream).

---

## Comment 14

> Username: quazeeee  
> Created at: 2021-07-02 11:46:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872937153  

Thank you all for such a detailed explanation. I have strengthened my understanding. In most of my projects, I always wrap client beast::socket to something similar to https://developer.mozilla.org/en-US/docs/Web/API/WebSocket .   
This question was based on another wrapper during which I found out about use_future completion handler and strand executor for wss_stream and was wondering about underlying changes.

---

## Comment 15

> Username: quazeeee  
> Created at: 2021-07-02 12:01:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-872944720  

Maybe last question on this topic. Can we call completion_future.wait() in the handler. I mean  
```  
on_callback() {  
completion_future = async_foo();  
completion_future.wait();  
}  
```  
Will it freeze forever?  
How it will behave in the case of 1 and several threads for io_service?  
Can you provide links to some documentation about customization points?

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-07-02 16:20:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-873114142  

the `use_future` token causes the asynchronous initiation function to return a future, who´s value will be set when an the asynchronous operation completes.  
  
In order to complete, it must make progress. This necessarily means that the asynchronous operation´s intermediate handlers must be associated either with a different executor (preferred), or at least another thread within the same executor (more dubious). By different I mean to the executor that cause the execution of the async_xxx function.  
  
use_future is generally used (rarely) as a bridge between threaded style programming and async-style, where the code calling x.async_wait(use_future).get() is running on a main thread and the executor associated with the asynchronous stream is making progress on another.  
  
In general, it is better (TM) not to mix the styles. Either do thread-based code or async code.

---

## Comment 17

> Username: stale[bot]  
> Created at: 2022-01-09 03:17:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2274#issuecomment-1008220773  

This issue has been open for a while with no activity, has it been resolved?
