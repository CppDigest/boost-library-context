# #2312 - Beast websocket SSL stream pending read assert seen [Closed]

> Username: gopalak  
> Created at: 2021-09-15 20:04:39 UTC  
> Updated at: 2022-09-24 05:09:58 UTC  
> Closed at: 2022-09-24 05:09:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2312  

beast version: BOOST_BEAST_VERSION 306  
  
I am unable to attach code here it s a big src repo. But this is what I am seeing  
  
I have 4 threads handling io_ctx completion  
I have mutex protected write and read trackers which get resent in on_read and on_write respectively  
  
on_read : processes read data, reset async read flag and calls do_read again  
  
I see an ASSEERT saying there is still a pending read even tho I am calling do_read from on_read and the expectation is that when I get called in on_read the pneding flag inside boost should be false but its not   
  
Hence I am seeing the assert  
  
ANy help is greatly appreciated  
  
========= Here is the print of state() from my bt ========  
      }  
    }  
  },   
  tick = 3,   
  pending = true,   
  timeout = false  
}  
(gdb) f 3  
#3  0x00007ffff69da252 in __GI___assert_fail (assertion=0x1e2e4c6 "! *b_", file=0x1e2e468 "/opt/code/ltt_0809/Vendors/boost_1.75/centos7/include/boost/beast/core/detail/stream_base.hpp", line=111, function=0x1e3eb40 <boost::beast::detail::stream_base::pending_guard::assign(bool&)::__PRETTY_FUNCTION__> "void boost::beast::detail::stream_base::pending_guard::assign(bool&)") at assert.c:101  
101       __assert_fail_base (_("%s%s%s:%u: %s%sAssertion `%s' failed.\n%n"),  
========================  
  
  
Here is some context --> I have mased our class names   
  
----------------------  
e inbin/lSERVER, CU 0x6fbd8d, DIE 0x9803c8>) Vendors/boost_1.75/centos7/include/boost/beast/websocket/impl/read.hpp:925  
#32 0x00000000006f9ea2 in session::async_read (this=0x2fc3268) at session.cpp:971  
#33 0x00000000006f02a5 in session::do_read (this=0x2fc3268) at session.cpp:216  
#34 0x00000000006f0815 in session::on_read (this=0x2fc3268, ec=..., bytes_xfred=253) at session.cpp:245  
  
This is the stack as u can see on_read is called from where I call do_read which calls async_read and inside there  
I see state() has pending to be true when its not supposed to be.  
  
No other thread was doing read at this time  
  
-------------------------------  
  
This is where the assert happens ===========  
  
#4  0x000000000044d112 in boost::beast::detail::stream_base::pending_guard::assign (this=0x7fffe76f7118, b=@0x2fc4bb8: true) at Vendors/boost_1.75/centos7/include/boost/beast/core/detail/stream_base.hpp:111  
111                 BOOST_ASSERT(! *b_);  
(gdb) l  
106                 // at the same time, without waiting for the first one  
107                 // to complete. For example, attempting two simultaneous  
108                 // calls to async_read_some. Only one pending call of  
109                 // each I/O type (read and write) is permitted.  
110                 //  
111                 BOOST_ASSERT(! *b_);  
112                 *b_ = true;  
113             }

---

## Comment 1

> Username: gopalak  
> Created at: 2021-09-15 20:07:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920339425  

@vinniefalco appreciate ur help

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-09-15 20:07:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920339826  

It may be that only one thread was performing a read, but if so, that thread has initiated a async_read without waiting for the first one to complete.  
  
This will result in undefined behaviour for any beast or asio stream.

---

## Comment 3

> Username: gopalak  
> Created at: 2021-09-15 20:10:00 UTC  
> Updated at: 2021-09-15 20:10:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920341213  

If u see the above stack trace -> the async_read is called from on_read which is the actual completion handler. So if I get called in the completion handler not sure why that earlier async op would be still pending @madmongo1

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-09-15 20:10:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920341445  

A more common cause of this is initiating an async_write before a previous one has completed.   
Most full-duplex applications will require the use of a write queue in order to avoid this.  
Furthermore, calling async_read and async_write on different threads at the same time is also UB.

---

## Comment 5

> Username: gopalak  
> Created at: 2021-09-15 20:11:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920342340  

> A more common cause of this is initiating an async_write before a previous one has completed.  
> Most full-duplex applications will require the use of a write queue in order to avoid this.  
> Furthermore, calling async_read and async_write on different threads at the same time is also UB.  
  
Really ?? The comment in the code says at any given time one async write and read can be happening. Are you telling me we can only have either a read or a write at any given time ?

---

## Comment 6

> Username: gopalak  
> Created at: 2021-09-15 20:13:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920343667  

I have a similar check for both async wite and async read. Only thing is that I treat each one can happen in tandem but ensuring only one of each type is active at any given time. So technically there could be an async write and an async read which can be happening at least per the docs and code comments. If that is not the case, I can use a single flag to track if either a write or read is happening and serialize them. but issue is an async_read can be waiting when I have data to write and delaying the write seems incorrect till we have data to read -  
Doesnt seem logical

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-09-15 20:13:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920343743  

>   
> Really ?? The comment in the code says at any given time one async write and read can be happening. Are you telling me we can only have either a read or a write at any given time ?  
  
No. You may have 0-1 reads, 0-1 writes and 0-1 close operations running simultaneously.  
They must all be initiated and make progress on the same logical strand.  
I am guessing that you are doing something wrong with your mutex and multiple threads.  
  
The correct way to use multi-threading with asio/beast is to use a strand.

---

## Comment 8

> Username: gopalak  
> Created at: 2021-09-15 20:14:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920344541  

I am using strand for my io_ctx in the thread. I just said I have 4 threads attached to the context and each session is using a strand on the io_ctx itself so that the completion handlers are serialized

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-09-15 20:15:58 UTC  
> Updated at: 2021-09-15 20:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920345167  

OK, so all async_reads and async_writes are:  
- being initiated on the same strand, and  
- making progress on that same strand  
?

---

## Comment 10

> Username: gopalak  
> Created at: 2021-09-15 20:16:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920345208  

> > Really ?? The comment in the code says at any given time one async write and read can be happening. Are you telling me we can only have either a read or a write at any given time ?  
>   
> No. You may have 0-1 reads, 0-1 writes and 0-1 close operations running simultaneously.  
> They must all be initiated and make progress on the same logical strand.  
> I am guessing that you are doing something wrong with your mutex and multiple threads.  
>   
> The correct way to use multi-threading with asio/beast is to use a strand.  
  
m_ws(asio::make_strand(io_ctx)) --> This is how I init my websocket stream - the io_ctx has its run called in N threads

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-09-15 20:16:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920345673  

How are you initiating your async_write operations?

---

## Comment 12

> Username: gopalak  
> Created at: 2021-09-15 20:17:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920346408  

> OK, so all async_reads and async_writes are:  
>   
> * being initiated on the same strtand, and  
> * making progress on that same strand  
>   ?  
  
Not sure I understand the q - when u say if all async ops are initiated in the same strand -> how do I ensure it or know. I am using which ever thread is executing that code path checks if there is a pending write before calling async_write and same for read. I am not sure I have full control of choosing the strand to init the op isnt it ?

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-09-15 20:19:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920347769  

Here is an example of a correctly written full duplex websocket connection.  
https://github.com/test-scenarios/boost_beast_websocket_echo/blob/9eab1f8a2454c670b8830de704718ba6a2f567c0/pre-cxx20/echo_server/connection.cpp#L123  
  
Note that every message to be written is :  
- marshalled onto the correct strand (the one the websocket was created with) using dispatch()  
- queued if there is already a write in progress.

---

## Comment 14

> Username: gopalak  
> Created at: 2021-09-15 20:20:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920347893  

> How are you initiating your async_write operations?  
  
I get data -> which gets into a buffer ->  
    the thread which is adding the data if gets a data packet from the buffer (as I need to hold till N bytes are received)  
    will return a package to be sent,  
  so which ever thread gets this (or a flusher thread which is running periodically) gets the data pkt and if it can  
  write sends the pkt else adds to a queue  
  So if there is a pending write when it gets done, in its completion handler I check if there data in queue and call async_write again and so on  
  
Similarly - for async_read

---

## Comment 15

> Username: gopalak  
> Created at: 2021-09-15 20:20:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920348224  

> Here is an example of a correctly written full duplex websocket connection.  
> https://github.com/test-scenarios/boost_beast_websocket_echo/blob/9eab1f8a2454c670b8830de704718ba6a2f567c0/pre-cxx20/echo_server/connection.cpp#L123  
>   
> Note that every message to be written is :  
>   
> * marshalled onto the correct strand (the one the websocket was created with) using dispatch()  
> * queued if there is already a write in progress.  
  
Issue is I am seeing the assert on read not write

---

## Comment 16

> Username: gopalak  
> Created at: 2021-09-15 20:21:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920348804  

And @madmongo1 my code is running as a client and not as a server

---

## Comment 17

> Username: madmongo1  
> Created at: 2021-09-15 20:22:03 UTC  
> Updated at: 2021-09-15 20:22:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920349190  

From what you have described to me, you are initiating reads and writes on the websocket from random threads. This will result in UB.  
If you follow my example you will see that I avoid this by using strands and dispatch() to get on to the correct executor.  
client/server is irrelevant. The principle is the same.

---

## Comment 18

> Username: madmongo1  
> Created at: 2021-09-15 20:23:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920350198  

Does any of your code look like this?  
```  
void  
connection_impl::send(std::string msg)  
{  
    net::dispatch(net::bind_executor(  
        stream_.get_executor(),  
        [self = shared_from_this(), msg = std::move(msg)]() mutable {  
            self->handle_send(std::move(msg));  
        }));  
}  
```

---

## Comment 19

> Username: madmongo1  
> Created at: 2021-09-15 20:25:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920351143  

in this function, regardless of which thread or executor it's called from, the actual work is transferred to the strand with which the websocket stream was associated at construction time.  
This means that the async_xxx is both initiated on the correct strand, and _makes progress_ on the correct strand.

---

## Comment 20

> Username: gopalak  
> Created at: 2021-09-15 20:25:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920351163  

So are u saying I cant call async_wrte and async_read from any thread ?? but have to dispatch it to the N threads that I have the io_ctx running ?  
and that is done via the disspatch ?

---

## Comment 21

> Username: madmongo1  
> Created at: 2021-09-15 20:27:12 UTC  
> Updated at: 2021-09-15 20:27:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920352469  

It sounds like you need to do some background reading of the ASIO documentation.  
`dispatch(e, f)` does this:  
```  
if(i am running on e already)  
  f();  
else  
  post(e, f);  
```

---

## Comment 22

> Username: madmongo1  
> Created at: 2021-09-15 20:28:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920353087  

Where `e` is any executor, which can also be a strand.

---

## Comment 23

> Username: madmongo1  
> Created at: 2021-09-15 20:29:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920353761  

https://www.boost.org/doc/libs/1_77_0/libs/beast/doc/html/beast/using_io/asio_refresher.html

---

## Comment 24

> Username: gopalak  
> Created at: 2021-09-15 20:29:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920353825  

> It sounds like you need to do some background reading of the ASIO documentation.  
> `dispatch(e, f)` does this:  
>   
> ```  
> if(i am running on e already)  
>   f();  
> else  
>   post(e, f);  
> ```  
  
I have been and have done - I probably misunderstood the need for dispatch and misunderstood MT safety of async_xxx calls from any thread

---

## Comment 25

> Username: madmongo1  
> Created at: 2021-09-15 20:29:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920354091  

>Concurrency  
I/O objects such as sockets and streams are not thread-safe. Although it is possible to have more than one operation outstanding (for example, a simultaneous asynchronous read and asynchronous write) the stream object itself may only be accessed from one thread at a time. This means that member functions such as move constructors, destructors, or initiating functions must not be called concurrently. Usually this is accomplished with synchronization primitives such as a mutex, but concurrent network programs need a better way to access shared resources, since acquiring ownership of a mutex could block threads from performing uncontended work. For efficiency, networking adopts a model of using threads without explicit locking by requiring all access to I/O objects to be performed within a strand.

---

## Comment 26

> Username: madmongo1  
> Created at: 2021-09-15 20:31:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920355522  

This is an async full-duplex client in the same demo repo. Note the use of dispatch to marshall work onto the correct _executor_.  
  
https://github.com/test-scenarios/boost_beast_websocket_echo/blob/master/pre-cxx20/chatterbox/connection.cpp#L125

---

## Comment 27

> Username: madmongo1  
> Created at: 2021-09-15 20:35:27 UTC  
> Updated at: 2021-09-15 20:36:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920357697  

For the record, this is almost certainly un-necessary:  
  
> I have 4 threads handling io_ctx completion  
  
If all your I/O is going through one network card, it is 99.9999% un-necessary.  
  
Async IO almost always runs faster on one thread.  
  
You can marshall long-running work onto a thread pool, and then marshall the resulting IO back onto your IO executor.

---

## Comment 28

> Username: gopalak  
> Created at: 2021-09-15 20:36:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920358448  

so   
  
> For the record, this is almost certainly un-necessary:  
>   
> > I have 4 threads handling io_ctx completion  
>   
> If all your I/O is going through one network card, it is 99.9999% un-necessary.  
>   
> Async IO almost always runs faster on one thread.  
  
Ok. Got it. Let me try the dispatch. I was using beast's bind_front_handlers but ur code seems to be using boosts bind APIs so my code may beed some work to use that.

---

## Comment 29

> Username: gopalak  
> Created at: 2021-09-15 20:39:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920360137  

I presume I can do something like:  
  
asio::dispatch(stream.get_executor(), bind_front_handler(...));

---

## Comment 30

> Username: madmongo1  
> Created at: 2021-09-15 20:40:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920360404  

> Ok. Got it. Let me try the dispatch. I was using beast's bind_front_handlers but ur code seems to be using boosts bind APIs so my code may beed some work to use that.  
  
Both are fine. That won't be your problem. The important thing is that the right part of your code are making progress on the correct executor.  
  
> asio::dispatch(stream.get_executor(), bind_front_handler(...));  
  
Yep

---

## Comment 31

> Username: madmongo1  
> Created at: 2021-09-15 20:41:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920361381  

these two are equivalent:  
  
```  
   asio::dispatch(e, std::move(handler));  
   asio::dispatch(asio::bind_executor(e, std::move(handler)));  
```

---

## Comment 32

> Username: madmongo1  
> Created at: 2021-09-15 20:43:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-920362091  

i.e. `dispatch` has a 1-argument form which will automatically dispatch to the _Associated Executor_ of `handler`.  
If `handler` does not have an _Associated Executor_, it will dispatch to the `system_executor` (which you almost never want).

---

## Comment 33

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2312#issuecomment-1008220733  

This issue has been open for a while with no activity, has it been resolved?
