# #2741 - How to use flat_buffer correctly [Closed]

> Username: romanholidaypancakes  
> Created at: 2023-09-19 13:04:50 UTC  
> Updated at: 2023-09-21 06:12:59 UTC  
> Closed at: 2023-09-21 06:12:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2741  

Currently I'm trying to use the beast asynchronous client, but I'm getting the following problem during testing:  
1. `flat_buffer` occasionally causes the program to crash  
2. `async_write` occasionally fails to deliver messages to the server  
  
I realized that I may have overlooked something, I am using this example: [websocket_client_async.cpp](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp), here is my simplified code  
  
[example.txt(updated)](https://github.com/boostorg/beast/files/12667465/example.txt)  
  
  
I tried looking for documentation, but I only found this page [boost__beast__flat_buffer](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__flat_buffer.html) that looked relevant, nothing Any help.  
  
I am using boost 1.83.0.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-19 13:24:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1725519837  

Are you sharing one buffer for reads & writes? That would be UB, you need two buffers.

---

## Comment 2

> Username: romanholidaypancakes  
> Created at: 2023-09-19 13:52:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1725625612  

> Are you sharing one buffer for reads & writes? That would be UB, you need two buffers.  
  
Yes, one for writing and one for reading?  
I have the reference code server-side code which is sharing a buffer, and  
  
  
https://github.com/boostorg/beast/blob/45d4f7f30640e3d10503abdcd53828aa9ab81bb5/example/websocket/server/async/websocket_server_async.cpp#L48  
  
Then I looked at all the use cases for buffers in the current repository (all using the same buffer) and I assumed it was thread-safe.  
  
---  
  
So is this the reason why some data does not reach the server?

---

## Comment 3

> Username: romanholidaypancakes  
> Created at: 2023-09-19 14:02:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1725656662  

I'm testing that there are still packets not reaching the server

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-09-19 22:50:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1726641177  

The example you link to doesn't use the buffer simultaneously and only clears it in the write.  
  
Do you have some updated code that doesn't use a shared buffer that I could look at?

---

## Comment 5

> Username: romanholidaypancakes  
> Created at: 2023-09-20 00:35:39 UTC  
> Updated at: 2023-09-20 00:43:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1726717907  

@klemens-morgenstern   
I have uploaded the code. I'm currently only testing it on the same local machine, so there shouldn't be a network reason.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-09-20 06:39:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1727060392  

Where did you upload the code? Maybe create a gist?

---

## Comment 7

> Username: romanholidaypancakes  
> Created at: 2023-09-20 09:10:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1727302145  

> Where did you upload the code? Maybe create a gist?  
  
[example.txt(updated)](https://github.com/boostorg/beast/files/12667465/example.txt)  
https://gist.github.com/romanholidaypancakes/b755c1609b068260833e95c9c7254777

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2023-09-20 11:13:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1727517911  

Your usage of the timer is causing UB, as you may have overlapping reads and writes. For example: you might be in an async_write, the timer triggers and clears the buffer during the write. That would be UB and might lead the your observation.  
  
You have to ensure that only one write & one read are happening at a given time.  
  
I personally like to use coroutines to achieve that. If you're on C++20 you should use asio::awaitable, before you can use asio::spawn with boost.context.

---

## Comment 9

> Username: romanholidaypancakes  
> Created at: 2023-09-20 11:55:51 UTC  
> Updated at: 2023-09-20 11:59:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1727578715  

> Your usage of the timer is causing UB, as you may have overlapping reads and writes. For example: you might be in an async_write, the timer triggers and clears the buffer during the write. That would be UB and might lead the your observation.  
>   
> You have to ensure that only one write & one read are happening at a given time.  
>   
> I personally like to use coroutines to achieve that. If you're on C++20 you should use asio::awaitable, before you can use asio::spawn with boost.context.  
  
I could try using coroutines, but I'd like to know what elegant solution is there for this example besides coroutines? Lock it? Or a timer calls read first and then calls write?  
  
Because, I want to use asynchronous websocket server.  
It is not difficult to see from my example that I want to reply to client messages regularly somewhere. I'm worried about performance issues if using coroutines on the server side.

---

## Comment 10

> Username: romanholidaypancakes  
> Created at: 2023-09-20 13:30:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1727737352  

For coroutines, I cannot send messages regularly to read messages. The real-world message model does not always need to be received immediately after sending. It is possible that the client still needs to send messages during this period because `async_read` is always Waiting leads to.  
  
  
I still like this, register a read callback, respond when there is a message, and provide a loop (timer) so that I can send any message at the time I need, which is what I want to do in the sample code. Is there a way for beast to do it?

---

## Comment 11

> Username: klemens-morgenstern  
> Created at: 2023-09-21 00:13:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1728581341  

You'd need to either us a channel (see asio::experimental::channel) or keep a flag to check if you triggered a read/write already. You can't just randomly trigger writes from multiple places.

---

## Comment 12

> Username: romanholidaypancakes  
> Created at: 2023-09-21 06:12:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2741#issuecomment-1728916016  

I'll try it. Thanks.
