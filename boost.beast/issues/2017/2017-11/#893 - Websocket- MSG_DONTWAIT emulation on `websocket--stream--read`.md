# #893 - Websocket: MSG_DONTWAIT emulation on `websocket::stream::read` [Closed]

> Username: mstanichenko  
> Created at: 2017-11-17 21:47:23 UTC  
> Updated at: 2018-06-09 20:13:04 UTC  
> Closed at: 2017-11-21 08:41:52 UTC  
> Url: https://github.com/boostorg/beast/issues/893  

Dear @vinniefalco,  
  
I would like `websocket::stream::read` function call to return immediately if there are no data and block if there are some data to process. In other words, I'd be glad to pass a flag which has the same meaning as `MSG_DONTWAIT` (see [man recv](http://man7.org/linux/man-pages/man2/recv.2.html)). My application is single-threaded and I am not allowed to spawn extra threads.  
  
Would you be so kind as to advice whether I can emulate such a behavior?  
  
Thank you very much in advance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-17 21:49:24 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345374995  

Are you calling `io_service::run()`?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-17 21:51:09 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345375401  

It sounds like you want to operate the websocket stream in non-blocking mode.

---

## Comment 3

> Username: mstanichenko  
> Created at: 2017-11-17 21:58:23 UTC  
> Updated at: 2017-11-17 21:59:07 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345376929  

> It sounds like you want to operate the websocket stream in non-blocking mode  
  
Yes, exactly.  
  
My application is single-threaded. I have an external dispatcher which calls my component. Once called, my component is supposed to check the data availability. I cannot block as there are other components to dispatch.  
  
May be I can call `recv` or `read` somehow and pass data further to `websocket`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-11-17 22:07:28 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345378836  

It is not a supported mode of operation. But you can try setting the socket into non-blocking mode and using beast's synchronous interfaces, and see if it works. You would just have to ignore the "would block" errors and try again later.

---

## Comment 5

> Username: mstanichenko  
> Created at: 2017-11-17 22:12:02 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345379802  

> But you can try setting the socket into non-blocking mode  
  
Could you please advise how one can assess the underlying socket?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-11-17 22:27:16 UTC  
> Updated at: 2017-11-17 22:29:21 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345382903  

>Could you please advise how one can assess the underlying socket?  
  
http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/reference/basic_socket/native_non_blocking/overload3.html  
  
To access the underlying socket, call `stream::next_layer()` or `stream::lowest_layer()`. This is explained in the documentation:  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/creating_streams.html#beast.using_websocket.creating_streams.non_owning_references

---

## Comment 7

> Username: mstanichenko  
> Created at: 2017-11-17 22:53:29 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345387598  

I took `websocket_client_sync_ssl.cpp` as a baseline.  
  
When I use this  
```c++  
ws.lowest_layer().native_non_blocking(true, ec);  
std::cout << "EC = " << ec << std::endl;  
```  
I can see the output `EC = system:0` but my process is permanently in `S`-state which means it's blocking.  
  
I also tried  
  
```c++  
ws.lowest_layer().non_blocking(true);  
```  
  
But I never got data with it

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-11-17 23:02:31 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345389149  

I don't think it will work with SSL

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-11-17 23:06:33 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345389834  

I'm sorry but this is not a supported mode. I don't see how it could be made to work with SSL.

---

## Comment 10

> Username: mstanichenko  
> Created at: 2017-11-18 08:53:27 UTC  
> Updated at: 2017-11-18 08:56:28 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345428595  

No problem, you already did a great job with such a nice library. Much appreciated.  
  
Would you be able to briefly describe what is wrong with a combination of non-blocking mode and SSL? Do you think I could apply simple changes to my local repository to make it work?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-11-18 09:24:33 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345430039  

When you perform a read in OpenSSL, the implementation can perform both reads and writes to fulfill that read. When you attempt an operation on a non-blocking socket that would block, an error is generated (EWOULDBLOCK). In order to make non-blocking work, the OpenSSL code has to detect that error and save its state in such a way that it can be reattempted later. Currently it does not do that, and doing so is non-trivial. For similar reasons the beast websocket code would require significant rewriting to work in non-blocking mode.

---

## Comment 12

> Username: mstanichenko  
> Created at: 2017-11-19 11:38:53 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345510097  

Dear @vinniefalco, thank you very much for your explanation.  
  
I am not sure that this will be implemented soon but may be at some point if more people want this functionality...  
  
Do you want me to close this issue? Or you prefer to keep it opened as a `feature request`?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-11-19 12:14:11 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345512013  

There's already an issue open for this: https://github.com/boostorg/beast/issues/445

---

## Comment 14

> Username: mstanichenko  
> Created at: 2017-11-20 09:17:43 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345635865  

I am not that close familiar with `boost` internals (yet) but is that really the case that using `async_read` with `null_buffers` will not spawn another user/kernel thread in the background?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-11-20 19:40:11 UTC  
> Updated at: 2017-11-20 19:49:16 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345806319  

It should not spawn another thread. In the updated version of Boost.Asio I believe `null_buffers` was removed and now you call `async_wait` on the socket directly.

---

## Comment 16

> Username: mstanichenko  
> Created at: 2017-11-21 08:41:51 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-345955232  

Thank you very much @vinniefalco, I am closing this now as a duplicate of #445   
For subsequent queries, a separate issue will be opened.

---

## Comment 17

> Username: TechnikEmpire  
> Created at: 2018-06-09 08:47:13 UTC  
> Updated at: 2018-06-09 08:49:20 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-395952152  

What about the underlying socket and the :: available() member method? Also, possibly could use a peek read on the underlying socket? I don't think the latter will interfere with the SSL stream object as the peek flag leaves the bytes untouched on the socket and (via layer selection) doesn't advance or change the state of the SSL stream itself. Not 100 percent sure if that is guaranteed state-independent though. Should be.  
  
Here's an example of passing a zero-length buffer (note: not a null_buffer) to the async read method, which forces immediate completion. Inside the handler, calling :: available () on the underlying socket (choose layer ie. next_layer or lowest_layer properly) where the result of ::available () should return the total number of bytes that can be read in a non-blocking fashion.  
  
https://stackoverflow.com/a/33556031  
  
Anyway it's almost 5 am and I've been armchair-commenting on your tickets so hopefully I'm not misunderstanding.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2018-06-09 13:23:14 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-395968882  

There are two problems. `ssl::stream` operations perform both reads and writes. And `websocket::stream` operations also perform both reads and writes. Rewriting the implementation of websocket stream to support non-blocking mode is not really practical. It might be doable for Beast's HTTP stream algorithms.

---

## Comment 19

> Username: TechnikEmpire  
> Created at: 2018-06-09 19:47:54 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-395994137  

I'm going to have to actually sit down and try building some beast samples. So far I've just been watching so I'm talking from a place of ignorance. I'll get familiar and come back to this.  
  
When you say that SSL stream perform both read and write, can you clarify the meaning and implication? I've seen that reference in other tickets and I'm trying to understand that.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2018-06-09 20:05:57 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-395995185  

> When you say that SSL stream perform both read and write, can you clarify the meaning and implication?   
  
Sure. An SSL **read** operation will perform reads, but it can also perform a write, as part of the session bookkeeping required by SSL. For example when the local peer does a read, the remote host can request a re-keying. In that case the local peer has to send a nonce. The same goes for SSL **write** operations, sometimes they need to perform reads.  
  
`beast::websocket::stream` functions similarly. For example, when beast tries to read a message from the websocket stream, and it receives a ping frame from the remote host, the implementation will automatically respond with a pong. This of course requires performing a **write**. As you can imagine, if the caller also has a write operation pending, beast has to do some rather complex juggling in its composed operations to make it all work (this is one of the cool features of the library).

---

## Comment 21

> Username: TechnikEmpire  
> Created at: 2018-06-09 20:13:04 UTC  
> Url: https://github.com/boostorg/beast/issues/893#issuecomment-395995592  

@vinniefalco Thanks for clarifying, makes sense now.
