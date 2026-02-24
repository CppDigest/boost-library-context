# #788 - BOOST_ASSERT(ws_.wr_block_ == tok_); [Closed]

> Username: ngladitz  
> Created at: 2017-09-21 12:02:39 UTC  
> Updated at: 2018-11-28 00:56:29 UTC  
> Closed at: 2018-11-28 00:56:29 UTC  
> Url: https://github.com/boostorg/beast/issues/788  

I am currently running Beast 116 but I think I've also seen the issue pop up with 112.  
  
It shows up infrequently. I have not been able to reproduce the issue consistently and I don't know what the root cause might be (it might be something I am doing).  
  
The assertion triggers in write.ipp:475:  
```C++  
    upcall:  
        BOOST_ASSERT(ws_.wr_block_ == tok_);  
```  
In the debug session I am looking at right now I see that `ws_.wr_block_.id_` is `0` while `tok_.id_` is `153`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-21 14:24:42 UTC  
> Updated at: 2017-09-21 14:27:32 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-331172631  

I'd like to know the following:  
1. Is your stream operating in the client or the server role?  
2. Do you have permessage-deflate turned on?  
3. What is your setting for auto-fragment?  
4. Are you calling `async_ping` or `async_pong` at all?

---

## Comment 2

> Username: ngladitz  
> Created at: 2017-09-21 15:07:25 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-331186137  

1. Server  
2. I haven't set it explicitly but the default seems no  
3. Not set explicitly but it seems to be on by default  
4. Not explicitly (so unless they are called implicitly somewhere; no)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-02 00:56:05 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-333419386  

I have studied the code and I cannot reproduce it, are you sure that you are not breaking any invariants? How many threads calling `io_service::run` do you have?

---

## Comment 4

> Username: ngladitz  
> Created at: 2017-10-02 06:17:28 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-333449130  

> I have studied the code and I cannot reproduce it, are you sure that you are not breaking any invariants? How many threads calling io_service::run do you have?  
  
Not sure at all. Would you suspect anything in particular that I might be breaking which I should take a closer look at?  
  
On my development system where I've seen the issue I've got 9 threads (`std::thread::hardware_concurrency() +1`) running `io_service::run`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-21 10:33:23 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-338381877  

Is this still happening?

---

## Comment 6

> Username: ngladitz  
> Created at: 2017-10-23 10:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-338611944  

Hard to say. I haven't seen it in a while but it was rare to begin with.  
Since I don't know how to reproduce it consistently I can't say for sure.

---

## Comment 7

> Username: ngladitz  
> Created at: 2017-10-23 13:12:38 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-338654190  

Ok I got lucky and apparently this still does happen.  
I've kept Beast up to date and am currently at version 123.

---

## Comment 8

> Username: glenfe  
> Created at: 2017-10-27 15:17:50 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340001128  

Nils, any chance the project in question is small enough (or non-confidential enough) that Vinnie could locally run or remote-debug it?

---

## Comment 9

> Username: ngladitz  
> Created at: 2017-10-27 15:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340010582  

@glenfe Sorry, neither in this case.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-10-27 16:04:57 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340014196  

Unfortunately there is not enough information for me to be able to either reproduce the defect or find the error through manual inspection of the code. If you want progress on this, I suggest you try to narrow it down. Does it happen when there is only one thread in the thread pool?

---

## Comment 11

> Username: ngladitz  
> Created at: 2017-10-27 16:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340017256  

I wish I could easily reduce this to a test case but as it is I can't even trigger it reliably and it occurs rarely (sometimes twice a day sometimes not for days) seemingly randomly. I'll try a single thread though.

---

## Comment 12

> Username: ngladitz  
> Created at: 2017-10-30 12:05:30 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340424778  

I think I actually managed to reproduce this outside of my actual project:  
https://gist.github.com/ngladitz/5c00a39df1531b7ab41fdef80b1419d0  
  
Steps I used to reproduce (requires CMake (>=3.4) and Boost (in my case 1.63); I did this on Ubuntu):  
```  
git clone https://gist.github.com/5c00a39df1531b7ab41fdef80b1419d0.git testcase  
cd testcase  
git clone https://github.com/boostorg/beast.git  
git -C beast checkout 885b9dfe0  
mkdir build  
cd build  
cmake -DCMAKE_BUILD_TYPE=Debug ..  
make  
./testcase  
```  
  
Output I get when running "./testcase":  
```  
listening on 39371  
testcase: /home/ngladitz/src/testcase/beast/include/boost/beast/websocket/impl/write.ipp:475: void boost::beast::websocket::stream<NextLayer>::write_some_op< <template-parameter-2-1>, <template-parameter-2-2> >::operator()(boost::beast::error_code, std::size_t, bool) [with Buffers = boost::asio::mutable_buffers_1; Handler = boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, Connection::start_websocket_write()::<lambda(const boost::system::error_code&, std::size_t)>, boost::asio::detail::is_continuation_if_running>; NextLayer = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `ws_.wr_block_ == tok_' failed.  
Aborted (core dumped)  
```  
  
The test case is brute force and does not terminate unless it hits the assert.  
It usually takes less than a minute for me to see the assert.  
  
I haven't seen the issue with a single thread in the pool.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-10-30 12:40:35 UTC  
> Updated at: 2017-10-30 12:49:22 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340432111  

The problem is that you are using two different strands, one for reading and one for writing! You can't do that :) The documentation is clear on this:  
  
_"For asynchronous operations, the application must ensure that they are are all performed within the same implicit or explicit strand."_  
  
and  
  
> **Thread Safety**  
> *Distinct objects:* Safe.   
> *Shared objects:* Unsafe.  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html  
  
Note that Asio sockets work the same way (and `ssl::stream`). They cannot be accessed from two threads simultaneously. If you are using this pattern of two strands in other code, I suggest you change it to use just one strand, as you are relying on undefined behavior.  
  
Thank God we figured this out because these websocket operations were very difficult to write and test, and make correct, and if this was a real bug this close to shipping it would be very bad!

---

## Comment 14

> Username: ngladitz  
> Created at: 2017-10-30 13:02:18 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340436866  

@vinniefalco I misunderstood the permission to run both an asynchronous read and write in parallel to mean that their completion handlers could as well. That was obviously wrong!  
  
Thank you!

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-10-30 13:07:33 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-340438122  

Ahh...you are referring to this part of the docs:  
  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety  
  
Looking it over, the example code probably needs to use a strand.

---

## Comment 16

> Username: Broekman  
> Created at: 2018-04-13 20:15:41 UTC  
> Updated at: 2018-04-13 20:23:49 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-381249475  

I'm encountering the same in debug builds when writing fast (after a few seconds runtime):  
  
> client: /usr/include/boost/beast/websocket/impl/write.ipp:468: void boost::beast::websocket::stream<NextLayer>::write_some_op< <template-parameter-2-1>, <template-parameter-2-2> >::operator()(boost::beast::error_code, std::size_t, bool) [with Buffers = boost::asio::mutable_buffer; Handler = boost::asio::executor_binder<micro::session::write()::<lambda(boost::system::error_code, std::size_t)>, boost::asio::strand<boost::asio::io_context::executor_type> >; NextLayer = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `ws_.wr_block_ == tok_' failed.  
> Aborted (core dumped)  
  
- Beast version 144.  
- Using a strand for all async operations.  
- Occurs in a stream operating in the client role.  
- Does NOT occur when the client is set to only one thread is calling io_context::run.  
- Occurring on both Arch Linux (GCC v7.3.1) and Windows with MinGW-W64 (GCC v7.3.0).  
- GDB operates to slow for it to occcur.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2018-04-13 21:07:47 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-381260909  

>I'm encountering the same in debug builds when writing fast (after a few seconds runtime):  
  
I believe this change fixes it:  
  
https://github.com/boostorg/beast/commit/726118468b940473e34410b48f8558c775eae179  
  
This fix was made on March 1st and it will be part of Boost 1.67.

---

## Comment 18

> Username: Broekman  
> Created at: 2018-04-13 22:03:31 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-381271591  

Thanks Vinnie, just tried it with Beast v167 and it works just fine now!

---

## Comment 19

> Username: vinniefalco  
> Created at: 2018-04-13 22:37:11 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-381276859  

\phew !! Thanks for letting me know!

---

## Comment 20

> Username: billlin0904  
> Created at: 2018-11-27 09:13:01 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-441983121  

> > I'm encountering the same in debug builds when writing fast (after a few seconds runtime):  
>   
> I believe this change fixes it:  
>   
> [7261184](https://github.com/boostorg/beast/commit/726118468b940473e34410b48f8558c775eae179)  
>   
> This fix was made on March 1st and it will be part of Boost 1.67.  
  
I am still working in BOOST 1.65.1, is this issule possible marge to Tag V124 ?

---

## Comment 21

> Username: vinniefalco  
> Created at: 2018-11-27 12:39:30 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-442044006  

Version 124 already has it:  
  
https://github.com/boostorg/beast/blob/v124/include/boost/beast/http/impl/read.ipp#L98

---

## Comment 22

> Username: vinniefalco  
> Created at: 2018-11-28 00:56:29 UTC  
> Url: https://github.com/boostorg/beast/issues/788#issuecomment-442277188  

This has been resolved for a while now...
