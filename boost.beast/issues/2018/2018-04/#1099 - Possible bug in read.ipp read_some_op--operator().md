# #1099 - Possible bug in read.ipp read_some_op::operator() [Closed]

> Username: bebuch  
> Created at: 2018-04-17 12:56:03 UTC  
> Updated at: 2018-05-19 02:30:50 UTC  
> Closed at: 2018-05-19 02:30:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1099  

In very rare cases I run into this BOOST_ASSERT:  
  
https://github.com/boostorg/beast/blob/b4cb4f1fd3d9d1eca90f44e59e1ca0b5260d0c13/include/boost/beast/websocket/impl/read.ipp#L628  
  
`ws_.status` is `status::failed` and I come from:  
  
https://github.com/boostorg/beast/blob/b4cb4f1fd3d9d1eca90f44e59e1ca0b5260d0c13/include/boost/beast/websocket/impl/read.ipp#L422  
  
`ws_.status` was in `L422` and at the beginning of `.operator()` (`L172`) already `status::failed`.  
  
I'm not completely sure it is a bug in Beast or in my own code.  
  
### Version of Beast  
  
```  
$ git log -1  
commit b4cb4f1fd3d9d1eca90f44e59e1ca0b5260d0c13  
Author: Vinnie Falco <vinnie.falco@gmail.com>  
Date:   Thu Mar 22 20:14:16 2018 -0700  
  
    Set version to 167  
```  
  
### Steps necessary to reproduce the problem  
  
The bug is trigged by a penetration test of my library based on Beast. I create a WebSocket Server and after that 50 threads every creating a client that connects to the server. Client and Server start to ping pong text messages and the clients a destroyed after a random time in the range of 0 to 100 ms.  
  
This process is repeated in a loop. I run into the bug around every 10 minutes, so it is very rarely and not simple to reproduce. I hope you can give clear a statement (bug in my code or in Beast) by looking into `L422` of `read.ipp`. If not I will provide more information how to compile and debug my code.  
  
### All relevant compiler information  
  
```  
$ g++ --version  
g++ (GCC) 8.0.1 20180410 (experimental)  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
With `BOOST_ASIO_HAS_STD_CHRONO` and `-std=c++14`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-18 23:31:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-382561279  

Thank you for the detailed report. I am investigating.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-24 00:00:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-383759772  

I am still looking at this, it is time consuming because all of the composed operations need to be analyzed for correctness.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-11 04:20:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-388255524  

I can't be 100% certain that the implementation correctly sets the `status_` variable

---

## Comment 4

> Username: adrianimboden  
> Created at: 2018-05-18 16:32:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390262317  

@bebuch Have you found any workaround in your case?  
  
I think I'm facing the exact same thing. The status is failed also.  
It happens since I refactored some server side code.  
  
I use async_read on the reader side and the assert is happening in a test where the client closes the websocket connection. When i use tsan, the error happens almost always.  
I added some output to look into it. The socket goes into error state after during the async_read call. Just before calling, the state is open.  
  
@vinniefalco If you have any place where should put some log output, I would be happy to provide you with the output.  
  
My configuration:  
* Clang 6.0  
* Boost 1.67  
* C++17

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-18 22:24:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390348286  

I am still investigating. To answer your question, I believe this bug is most likely in Beast. The interaction between the four composed operations (read, write, ping, and close) is very complex and the `status_` variable is not something that I invested a lot of resources in getting right. So it is likely to contain at least one bug.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-05-18 23:44:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390359250  

I have studied the code and concluded that the assert is simply wrong. If you remove the assert in your beast implementation file, it should work correctly.

---

## Comment 7

> Username: adrianimboden  
> Created at: 2018-05-18 23:49:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390359904  

I just found out, that the error occurs because i use async_read and async_write without a strand.  
Is that basically supported or not?  
  
Are the internals of async_read and async_write supposed to be interleavable?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-05-18 23:53:58 UTC  
> Updated at: 2018-05-18 23:54:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390360387  

> Are the internals of async_read and async_write supposed to be interleavable?  
  
Far from it! You need either an implicit strand (only one thread calling `io_context::run`) or explicit strand.  
  
The same goes for asio sockets - they are not Thread Safe. Don't confuse thread safety with asynchronous. You cannot access I/O objects like sockets (and correspondingly, websocket streams) from more than one thread simultaneously.  
  
You can have async_read and async_write both pending at the same time though. See:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety

---

## Comment 9

> Username: adrianimboden  
> Created at: 2018-05-18 23:58:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390360952  

I have only one thread calling io_service::run. But the async_write and async_read functions did interleave anyway.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-05-18 23:59:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361025  

> the async_write and async_read functions did interleave anyway.  
  
What does that mean, interleave?

---

## Comment 11

> Username: adrianimboden  
> Created at: 2018-05-18 23:59:24 UTC  
> Updated at: 2018-05-18 23:59:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361028  

Can't the methods interleave, because the coorutine parts can interleave?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-05-19 00:00:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361139  

I'm not following, what do you mean interleave?

---

## Comment 13

> Username: adrianimboden  
> Created at: 2018-05-19 00:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361355  

I did debug the following scenario:  
1. Call async_read  
2. Call async_write  
3. async_write has an error (broken_pipe, because client disconnected)  
4. async_write completion handler reports broken pipe  
5. async_read throws the assertion  
  
All of that happens in the same thread.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-05-19 00:02:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361471  

The assertion not valid, you should remove the line from read.ipp.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-05-19 00:03:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361579  

>1. Call async_read  
> 2. Call async_write  
  
Are you making these calls from the same thread that is invoking `io_context::run`?

---

## Comment 16

> Username: adrianimboden  
> Created at: 2018-05-19 00:04:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361649  

Yes, exactly.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2018-05-19 00:04:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361680  

That should work.

---

## Comment 18

> Username: adrianimboden  
> Created at: 2018-05-19 00:04:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390361778  

Yes, it probably will when I remove the assert. I'll try that now

---

## Comment 19

> Username: adrianimboden  
> Created at: 2018-05-19 00:16:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390363107  

Ok, allright. When I apply the patch from above, everything works as expected. Perfect :smile:

---

## Comment 20

> Username: vinniefalco  
> Created at: 2018-05-19 00:16:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1099#issuecomment-390363203  

This fix will go into 171
