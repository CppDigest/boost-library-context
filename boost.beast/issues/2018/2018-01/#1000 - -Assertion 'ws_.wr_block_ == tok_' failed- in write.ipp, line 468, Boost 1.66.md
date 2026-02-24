# #1000 - "Assertion 'ws_.wr_block_ == tok_' failed" in write.ipp, line 468, Boost 1.66 [Closed]

> Username: fpingas  
> Created at: 2018-01-29 16:44:56 UTC  
> Updated at: 2018-02-16 20:38:35 UTC  
> Closed at: 2018-02-16 20:38:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1000  

Hi,  
I am trying to establish a websocket session, that keeps a pending async_read command, while processing a queue of write requests, one async_write at a time.  
  
I keep running into this assert even with only a single thread running the pertinent io_context. I have tried to keep all the calls to these asynchronous operations on explicit strands, but I still run into the same error.  
  
It is, to my understanding, possible to maintain these two concurrent operations, given the correct use of strands. Still, I have been unable to make it work as I expected. I would appreciate some guidance in this issue.  
  
This happens with Beast 144 with Boost 1.66, on Windows 10 with mingw 5.3.0 32bit.  
  
I can reproduced the issue with the following routine:  
[https://gist.github.com/Pinguinha/f282e049102cec63f28053e3622166bb](https://gist.github.com/Pinguinha/f282e049102cec63f28053e3622166bb)  
  
Thanks in advance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-29 17:34:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1000#issuecomment-361323032  

Just a note, in your function `onWrite` if you just always `post` the string to the strand, and then append to the queue or initiate a send inside the posted completion handler, you won't need a mutex. Strands already come with a mutex, you're adding another one on top of it.  
  
I am investigating, thanks for the report.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-01-30 01:53:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1000#issuecomment-361450449  

Update: I was able to build the example program after fixing a trivial compilation error in the construction of the endpoint. I also get the assert. Nothing looks wrong with your program at first glance, but the assert means an invariant has been broken so I am continuing to study it - thanks!

---

## Comment 3

> Username: djarek  
> Created at: 2018-01-31 07:05:21 UTC  
> Updated at: 2018-01-31 07:05:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1000#issuecomment-361842968  

@Pinguinha   
Probably not the root cause, but after a 5 minute glance I noticed you might have a data race in `main()` in line 228:  
```cpp  
client1->socket().async_close(boost::beast::websocket::close_code::normal, [](auto){});  
```  
  
Looks like the access to this socket is not protected with the strand used elsewhere in `WssSession`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-31 07:44:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1000#issuecomment-361850073  

I get the assert before the `async_close` ever has a chance to run but you're right, it needs a strand (implicit or explicit).

---

## Comment 5

> Username: bostjanv  
> Created at: 2018-02-02 12:03:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1000#issuecomment-362568903  

I think I've stumbled upon the same issue. I made small modifications to websocket_client_async.cpp and websocket_server_async.cpp examples which are able to trigger the assert. Please take a look:  
  
https://gist.github.com/bostjanv/5f7d6a6867f6e7e022176494730780db  
https://gist.github.com/bostjanv/7240bbd40976e35696942133da684bec  
  
I'm using Beast from Boost 1.66 and gcc 7.2.0.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-02-15 18:10:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1000#issuecomment-366013392  

This took way longer than it should have to figure out but now we know the problem and it will be fixed in Boost 1.67.0 (and the master/develop branches here). Thanks for reporting this and thanks a ton for the helpful example program, without which I could not have tracked down the problem. If you want a cheap temporary fix, change the `token` class in `<boost/beast/websocket/stream.hpp>` to use `unsigned long` instead of `char`. I will publish a solid fix shortly.

---

## Comment 7

> Username: bostjanv  
> Created at: 2018-02-15 18:45:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1000#issuecomment-366023185  

This is good news! Thanks for taking the time and figuring it out.
