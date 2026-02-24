# #1093 - Question on pong message and user-initiated write [Closed]

> Username: ezzt  
> Created at: 2018-04-10 19:39:15 UTC  
> Updated at: 2018-06-17 15:10:40 UTC  
> Closed at: 2018-06-17 15:10:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1093  

## Version of Beast  
`#define BOOST_BEAST_VERSION 144`  
(Boost overall version 1.66.0)  
  
## Details  
This [Beast Websocket Doc](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/control_frames.html  
) has below statement regarding pong control frame:  
> A consequence of this automatic behavior is that caller-initiated read operations can cause socket writes. However, these writes will not compete with caller-initiated write operations.  
  
So if I do something like below in the websocket client:  
- register async_read on the websocket, which is on the boost ioservice thread;  
- do write (either sync write or async_write) from a different user thread  
  
Will my own write and the automatic pong write be thread-safe? If not, how do I make sure my own write doesn't conflict with automatic pong write?  
  
Thanks!

---

## Comment 1

> Username: djarek  
> Created at: 2018-04-10 19:56:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-380227049  

It's important to differentiate concurrency and parallelism when we talk with the networking model in ASIO (and by extension in the upcoming Networking TS).  
  
Concurrent execution of operations means that operations may be in progress at the same time (regardless of the number of threads involved). In other words you may call  
```cpp  
stream.async_read_some([](/*...*/){});  
stream.async_write_some([](/*...*/){});  
stream.async_ping([](/*...*/){});  
```  
On the other hand, you're not allowed to invoke those calls from 2 different threads at the same time. Access to I/O objects in the ASIO model requires user-provided synchronization (preferably via a strand).  
  
Note that `stream` does not allow more than one async operation of a particular type (e.g. 2x `async_write`) to be executing at the same time, i.e. the second `async_write` must not be called before the CompletionHandler passed to the first one is executed.

---

## Comment 2

> Username: ezzt  
> Created at: 2018-04-10 22:53:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-380272807  

I think I got what you mean. Just want to make it more clear under my scenario:  
  
What I want is similar to post #1092, and I think it is a fairly common user case. What I want is to have one thread (which is default boost io_service::run) that does continuous async_read (i.e schedule another read at the end of readHandler). And another (only one) user thread does write to websocket on demand. And I want to make sure my write operation is intact/thread-safe. I think this is normally okay for a regular TCP socket, but not for beast websocket, because beast does pong write in read operation, and thus my question.  
  
For beast websocket, can I do following? from my user thread, wrap the write (a blocking write) into a handler, and post it to io_service strand. In this way, I can post multiple write handler to strand, and don't worry about keeping only one outstanding async_write.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-10 23:40:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-380280949  

You will need to implement your own queue for outgoing messages.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-05-10 23:42:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-388218072  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-11 01:08:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-388229848  

To clarify, no you cannot use the `io_context` as an outgoing message queue. You need to implement your own queue and make sure only one write is outstanding. The pongs will be handled transparently, you don't need to worry about them. A sample chat server which demonstrates this is here: https://github.com/vinniefalco/CppCon2018

---

## Comment 6

> Username: ezzt  
> Created at: 2018-05-11 14:11:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-388375401  

What I end up doing is, from my user thread, post a handler into default io_service::run thread, I copy my data into that handler, and do synchronous write in the handler. I didn't use a queue + async write because I dont want to maintain a thread-safe queue plus making sure only one outstanding async write. My write is small, so I hope it should finish soon, not blocking for too long.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-05-11 14:12:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-388375747  

Mixing synchronous and asynchronous operations on `websocket::stream` can produce undefined behavior.

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-06-10 15:06:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-396056296  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2018-06-17 15:10:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1093#issuecomment-397885245  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
