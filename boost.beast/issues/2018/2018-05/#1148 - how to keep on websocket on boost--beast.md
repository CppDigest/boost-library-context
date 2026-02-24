# #1148 - how to keep on websocket on boost::beast? [Closed]

> Username: caromdreamer  
> Created at: 2018-05-31 06:03:10 UTC  
> Updated at: 2018-08-15 15:23:14 UTC  
> Closed at: 2018-08-15 15:23:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1148  

I referenced here(https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/async/websocket_client_async.cpp) documents about boost::beast websocket-async.  
  
I removed "ws_.async_close(websocket::close_code::normal..." on on_read function in order to avoid disconnection.  
  
But beast websocket is disconnected by ending on_read.  
```  
std::make_shared<session>(ios)->run(host, port, text);  
ios.run(); // I want to keep running it until explicit calling close.  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-31 20:34:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-393670744  

If you want to keep `run()` from returning, you have to make sure you always have at least one asynchronous operation in progress.

---

## Comment 2

> Username: caromdreamer  
> Created at: 2018-06-01 04:13:28 UTC  
> Updated at: 2018-06-01 04:14:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-393752822  

Thanks. I have understood your mention.  
  
If I wait message from server in websocket, how can I write code what I want? In this situation, Websocket client must be able to wait for receiving message, must be able to send message to server whenever I want.   
I want to know typical code for above goals.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-01 04:29:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-393754558  

> Websocket client must be able to wait for receiving message  
  
Make sure you always have one call to `async_read` active  
  
> must be able to send message to server whenever I want  
  
You will need to write a queue. When you want to send a message, add it to the queue. If the queue was previously empty, then send the front of the queue now. Otherwise, wait for the current message to be sent. When it is done remove it from the queue and send the next message, until the queue is empty.  
  
In order to send messages from other threads, use `boost::asio::post` to submit the work to the implicit or explicit strand.  
  
This chat server might give you ideas:  
https://github.com/vinniefalco/CppCon2018

---

## Comment 4

> Username: caromdreamer  
> Created at: 2018-06-01 09:56:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-393833586  

I am appreciated to you.

---

## Comment 5

> Username: ghost  
> Created at: 2018-06-06 01:55:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-394914787  

> https://github.com/vinniefalco/CppCon2018  
  
Is there a video of this talk somewhere?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-06-06 11:12:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-395032116  

> Is there a video of this talk somewhere?  
  
There will be a video in October, after I give the talk in September at CppCon.

---

## Comment 7

> Username: TechnikEmpire  
> Created at: 2018-06-09 07:14:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-395946896  

You can also construct a 'work' object around the io context to prevent the reactor from exiting when the work queue is empty, which comes in handy when your design doesn't require you to always keep one open request.  
  
https://stackoverflow.com/a/35945722  
  
Note that io_service is now called io_context in asio so it should be io_context::work.

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-07-09 07:22:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-403384106  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-07-09 12:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-403470147  

> it should be io_context::work  
  
Actually, `io_context::work` is provided for legacy reasons, the type `executor_work_guard` is the preferred choice.

---

## Comment 10

> Username: TechnikEmpire  
> Created at: 2018-07-09 13:35:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-403481110  

Thanks for the correction, I'm a bit out of date on this stuff I see. :)

---

## Comment 11

> Username: stale[bot]  
> Created at: 2018-08-08 14:29:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-411426243  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 12

> Username: stale[bot]  
> Created at: 2018-08-15 15:23:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1148#issuecomment-413232756  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
