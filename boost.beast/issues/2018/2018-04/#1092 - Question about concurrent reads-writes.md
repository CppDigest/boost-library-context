# #1092 - Question about concurrent reads/writes. [Closed]

> Username: JunielKatarn  
> Created at: 2018-04-05 04:42:24 UTC  
> Updated at: 2022-12-08 02:22:01 UTC  
> Closed at: 2018-05-10 04:14:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1092  

### Version of Beast  
144  
  
### Details  
  
Hi. I'm writing a WebSocket client that sends and receives messages, handling both operations continuously and independently.  
(From what I see in the repository's examples, it seems that a write operation triggers a single read operation).  
  
This is the general idea:  
1. Client connects to server.  
1. Client receives and processes incoming messages (i.e. printing, logging).  
1. Client sends messages by user interaction.  
  
Essentially, the same principle of a chat client (read and write operations should be fully independent, and never block the app if there is no incoming data).  
  
From the [Boost docs](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/design_choices/comparison_to_zaphoyd_studios_we.html), looks like there is no "OnMessage" or "MessageReceived" event handler in the Beast APIs, but rather, it's up to the app author to schedule the `read`/`async_read` operations as needed.  
  
What is the best strategy for this kind of bi-directional workflow using Beast?  
- Two different `io_context` instances, one for reading, one for writing?  
- One `io_context`, but two threads (one for reads, one for writes)?  
  - Can the operations be serialized using a strand?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-05 06:07:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-378829801  

You only need one `io_context` and one thread (this is an "implicit strand"). If you use multiple threads then you will need an explicit strand (e.g. `boost::asio::strand`). Use the example server as a starting point, it demonstrates the use of asynchronous APIs.

---

## Comment 2

> Username: rberlich  
> Created at: 2018-04-05 11:39:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-378907259  

Hi Juniel, I have collected what I learned about Beast in a sample program [here](https://github.com/rberlich/Estray). It produces packages of random numbers on the server side, serializes them and ships them to the client, where they are sorted and sent back to the server. This implements a simple protocol between client and server. The status of this is "works for me" -- let me know if you find something that doesn't work. Kind Regards, Beet

---

## Comment 3

> Username: JunielKatarn  
> Created at: 2018-04-06 00:41:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-379116858  

Thanks, @rberlich. I'm taking a look at it :)  
  
> You only need one io_context and one thread (this is an "implicit strand")  
  
@vinniefalco So, I can have reads and writes running concurrently (albeit, only one read and one write at a time) with a single invocation to io_context.run()?  
The behavior I'm trying to achieve should continuously listen to messages as a client, independently from when messages are sent to the server. Regarding that:  
- What phase of the websocket stream's life cycle should post the read operations?  
- What is the correct way to post concurrent reads? `boost::asio::post`?  
  
Thanks!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-04-06 01:01:14 UTC  
> Updated at: 2018-04-06 01:01:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-379119463  

We need to be careful when using the term "concurrent" because in the context of a multi-threaded application, it can be confusing. For example, it may seem counterintuitive but Asio sockets and streams are **not thread safe**.  And by extension, `beast::websocket::stream` objects are similarly **not thread safe** (this is indicated in the documentation). What does this mean? Simply that member functions **must not be called concurrently**. Or to put in clear terms, two threads may not call any member functions of `beast::websocket::stream` simultaneously. This is why you need either an implicit strand (only a single thread calling `io_context::run`) or an explicit strand (an object of type `boost::asio::strand`).  
  
This does not mean that only one asynchronous operation can be active at once. It just means that you cannot call two initiating functions (names that start with `async_`) on the same object from different threads simultaneously. We use the term "pending" to indicate an asynchronous operation which has been started through the invocation of an initiating function and has not yet completed, to distinguish from the term "concurrent" which is used to talk about thread safety. For example, after a call to `beast::websocket::stream::async_read` returns, there will be a read operation pending. There may only be one read operation pending. However, you can have both a read and a write operation pending simultaneously. In fact you can have all four websocket operations (read, write, ping/pong, and close) pending at the same time. This is described here:  
https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety  
  
Users of Beast are expected to already understand Asio's asynchronous model, I can't possible be tasked with trying to teach the whole world (but I don't mind answering questions when I can). Beast doesn't innovate here, it simply follows Asio's model. Every feature and requirement found in Asio will also be found in Beast.  
  
That said, I can try to answer your questions:  
  
* What phase of the websocket stream's life cycle should post the read operations?  
  
You can only call read and write operations after performing a successful websocket handshake (`stream::async_handshake` or `stream::async_accept`).  
  
* What is the correct way to post concurrent reads? boost::asio::post?  
  
Use `websocket::stream::async_read`. However, "concurrent read" is a misleading term. Do you mean that you wish to have two reads pending at once? That is not supported. Or do you mean a read that is concurrent with other operations? In that case, you simply call `async_read` when you know either that it is the first time calling `async_read`, or you have already received a completion from a previous call to `async_read`. As you might guess, programs typically call `async_read` again from the completion handler  passed to `async_read`.

---

## Comment 5

> Username: JunielKatarn  
> Created at: 2018-04-09 23:58:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-379929367  

> I can't possible be tasked with trying to teach the whole world  
  
Not the intention at all!  
I admit being new to Boost ASIO, but not fully ignorant of the concepts.  
  
Regarding "concurrent read", I mean reads being scheduled in a "separate" workflow from writes (writes are user-interactive, reads should happen continuously).  
  
@rberlich 's sample repo throws some light on my problem. (Submitted an issue with questions to it).  
It appears that I will need two threads (to keep interactive writes (and eventual close) independent from continuous reads).  
  
I'll close this for now, but will issue a last update when I get this working, in case someone else finds my case useful.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-04-10 00:08:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-379931615  

>It appears that I will need two threads  
  
You can do continuous reads and interactive writes with one thread.

---

## Comment 7

> Username: JunielKatarn  
> Created at: 2018-04-10 00:18:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-379933929  

Yikes! That's where I get stuck.  
  
I'm guessing I need to make sure there is always one, and just one `read_async` posted, until I call `async_close`.  
  
I need to start 'waiting for messages' as soon as the handshake is done. Say, calling a method `StartRead()`.  
In `StartRead`, I issue an `async_read`, and inside its completion handler, a subsequent `StartRead` call assuming no error code is found and the input buffer has been consumed.  
  
This has ended up leaving the app stuck in the `StartRead` sequence, without giving the opportunity to issue the interactive writes.  
I'll post the code later, for reference.  
  
Thanks.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-04-10 00:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-379934484  

Yes you got it all correct, although I can't imagine why your app is "stuck." If you want to perform a write, just call `async_write` from the correct context. If you are on a foreign thread use `boost::asio::post` to get onto the implicit or explicit strand.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-04-10 00:23:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-379934684  

Your connection object needs to also make sure there is at most only one call to `async_write`. For this you will need a bool or something like that. If you want to perform a write (i.e. to send a new message) and a write is already pending, then you will need to store the new message in an outgoing message queue so when the current write completes, you can pull the next one off the queue and begin sending it.

---

## Comment 10

> Username: stale[bot]  
> Created at: 2018-05-10 00:51:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-387917653  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 11

> Username: harryofskyrim  
> Created at: 2019-03-27 08:44:42 UTC  
> Updated at: 2019-03-27 08:44:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-477040827  

@JunielKatarn   
  
> I'll post the code later, for reference.  
  
Was there the code after all? Did it work?

---

## Comment 12

> Username: JunielKatarn  
> Created at: 2019-03-27 12:13:26 UTC  
> Updated at: 2019-03-27 12:14:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-477123697  

> @JunielKatarn  
>   
> > I'll post the code later, for reference.  
>   
> Was there the code after all? Did it work?  
  
@harryofskyrim   
Yes, it did work.  
I haven’t published the sample, though.  
I can create a gist later, if you are interested.

---

## Comment 13

> Username: harryofskyrim  
> Created at: 2019-03-28 07:25:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-477479754  

@JunielKatarn How much later?

---

## Comment 14

> Username: JunielKatarn  
> Created at: 2019-03-28 07:26:19 UTC  
> Updated at: 2019-03-28 07:26:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-477480063  

@harryofskyrim likely tonight. I'm currently away from computer.

---

## Comment 15

> Username: JunielKatarn  
> Created at: 2019-03-29 00:30:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-477819739  

@harryofskyrim See https://github.com/Microsoft/react-native-windows/blob/rnwcpp-preview/RNWCPP/Desktop/WebSocket.cpp . Work in progress, but it implements the semantics of a generic WebSocket client on top of Beast.

---

## Comment 16

> Username: pSN0W  
> Created at: 2022-10-25 12:42:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-1290493270  

@JunielKatarn I am facing a similar issue. Can you post the code. The above link doesn't work.

---

## Comment 17

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-08 02:07:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-1341875067  

please lmao

---

## Comment 18

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-08 02:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-1341875208  

need this to skid and paste off of

---

## Comment 19

> Username: JunielKatarn  
> Created at: 2022-12-08 02:18:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-1341885379  

> need this to skid and paste off of  
  
Oh, hi. That cold is old and I replaced it with a WinRT implementation, but I should be able to dig it out.

---

## Comment 20

> Username: JunielKatarn  
> Created at: 2022-12-08 02:22:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1092#issuecomment-1341888916  

@pSN0W @tyyykkkeeessshhaaaa   
  
https://github.com/microsoft/react-native-windows/blob/react-native-windows_v0.70.7/vnext/Desktop/BeastWebSocketResource.h  
https://github.com/microsoft/react-native-windows/blob/react-native-windows_v0.70.7/vnext/Desktop/BeastWebSocketResource.cpp
