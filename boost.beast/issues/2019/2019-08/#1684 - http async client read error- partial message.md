# #1684 - http async client read error: partial message [Closed]

> Username: lalalacampanella  
> Created at: 2019-08-16 02:11:14 UTC  
> Updated at: 2020-09-17 07:01:22 UTC  
> Closed at: 2019-09-25 02:10:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1684  

I try to use beast http async client to handle http request with tars/jce(https://github.com/TarsCloud/Tars), which likes protobuf, can serialize message into binary char *.  
however when server set a whole file into http response body(about 5mb or more), it occasionally occur read error: partial message, when i use multi thread with multi request in the same time, it occurs frequently(over 50%)  
  
it's hard to put my code here, it's a litter huge and complicated  
  
I use boost 1.70.0  
   code modified from example/http/client/async   
   use http::response<http::string_body>  
  
I want to know if it is correct to use a string body(because in fact i am transfering a binary content), but when i try to use buffer body ,it always comes to error::need_buffer, when i try to use dynamic body, i use beast::buffers_to_string to convert response into string, but when i try to use jce to deserialize the response into object, it seems that jce says the response is invalid to be deserialized.  
  
if it's nothing to do with the response body type, what situation can it be? what should I do to avoid this error?

---

## Comment 1

> Username: lalalacampanella  
> Created at: 2019-08-16 08:47:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-521936885  

It seems that when I perform with high concurrency, It occurs very frequently(althouth they are in different io_context)  
to be more specific, when I use four io_contexts, each run three threads, each holds ten sessions loop running with 5mb response body, It happends almost 100%. but when I reduce io_context to one or two, the probability of occurrence drops to 10%.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-08-16 15:26:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-522049315  

Are you using strands?

---

## Comment 3

> Username: lalalacampanella  
> Created at: 2019-08-18 07:52:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-522299937  

yes, emmm... sorry, I am a newbie, is there anything wrong with this?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-08-18 15:46:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-522332567  

You must use a strand if you have multiple threads calling `io_context::run`. You usually don't need more than one `io_context`, so I'm wondering why you have four. I suggest you only create one I/O context, and only have one thread call `io_context::run`. If your program then works correctly, it means the problem is unsafe concurrent access to objects, for which a `strand` is the solution.

---

## Comment 5

> Username: lalalacampanella  
> Created at: 2019-08-19 01:10:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-522377441  

My goal is to build a download client for some very big data(over 100G for one task), At the beginning I used one io_context for everything, but when I need to run multiple tasks at the same time, it happens to be very difficult to handle everything in one. (eg: pause, restart, and other operation that I need to let each task not interact with each other).   
  
for example, with 5mb per request, I have to send 20000 requests for 100 gb files, I can't just let so many request sent at the same time, so I build a pool to cache waiting request, once one request finish then take another from the pool and keep going. That makes it hard to let other tasks keep going.  
  
I think a easy way is seperate each tasks' io_context, rather than create a strong allocation algorithm for tasks' requests share one io_context.  
  
I already used a strand, like the example, but it still occurs read error partial message.  
  
Anyway, I'll try to use one thread for each io_context for a try, thank you for you reply!

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-09-18 01:35:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-532480589  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-09-25 02:10:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-534819668  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 8

> Username: 9chu  
> Created at: 2020-09-17 02:14:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-693765883  

+1  
  
Got `partial message` error reading request body under high concurrency, with two threads and one i/o context.  
Each request body is larger than 100k bytes.  
And each async operation is been done by the `asio::post` method posting to the `beast::tcp_stream::get_executor()`, not knowing why and how to debug this issue.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-09-17 07:01:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1684#issuecomment-694001797  

Since you have 2 threads, my first and strongest suspiscion is that you have a data race. It is essential that two threads do not interact with the same socket/stream at once.  
  
The easiest way to avoid this is to use a `strand` as the local executor of each connection_impl. When you construct the underlying socket, make sure you pass the strand as the executor for that socket, not the io_context::executor.   
  
This way, all completion handlers of async operations on the stream will complete by being invoked by the strand (sequentially) rather than by the executor (in parallel).
