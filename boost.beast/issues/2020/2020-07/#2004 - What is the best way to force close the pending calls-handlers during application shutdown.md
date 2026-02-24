# #2004 - What is the best way to force close the pending calls/handlers during application shutdown [Closed]

> Username: ashjas  
> Created at: 2020-07-02 10:54:48 UTC  
> Updated at: 2020-08-23 09:00:36 UTC  
> Closed at: 2020-08-23 09:00:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2004  

Beast version 1.71  
  
I am using the sync aswell as async ways for REST communication.  
  
I need to close the application as soon as possible even if some of the network calls/handlers may be busy in read/write.  
  
The beast samples shows the graceful way of shutting down the socket by calling shutdown() on the underlying socket.  
  
But i am confused by whats documented here: [basic_socket.hpp](https://github.com/chriskohlhoff/asio/blob/574227a8591cb67c9575a72062daf5388feef470/asio/include/asio/basic_socket.hpp#L500)  
  
it says to do a shutdown before close().  
  
So whats the right way, to close/shutdown the socket?  
  
And how is this forcefull close process different in sync vs async process?  
do i have to post the close/shutdown call in async flow or a direct call would suffice?  
And i would assume since in sync flow there is no io_context.run(), we always issue a direct call to the  
close()/shutdown() methods.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-02 11:29:35 UTC  
> Updated at: 2020-07-02 11:30:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-652951502  

calling shutdown() followed by close() on the socket is correct insofar as performing an orderly shutdown on the underlying TCP transport.  
  
It will cause any outstanding async IO to complete with errors.  
  
When you say "close the application", the quickest way would be to call `std::exit()`. Although this is the nuclear option, it might be what you want.

---

## Comment 2

> Username: ashjas  
> Created at: 2020-07-02 13:31:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-653007474  

by quickest way, i mean that the application that integrates beast, has its working exit process. I just need to make sure, when that process is followed, the network calls dont unnecessarily delay that process. When the application closes, its ok, for the pending operations to fail, as the application is going away anyway. So in that sense, std::exit() is not what i was looking for.  
  
So when the beast samples, only do a shutdown and not a close() what state does that leave the socket in?  
  
And is the close() on a socket implicit, when the session containing the stream is destructed?  
  
Also, do i need to net::post/wrap with a strand the shutdown()/close() operations for async flows?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-07-02 14:40:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-653046539  

Hi @ashjas ,  
  
In general, Asio follows the BSD sockets library model. The online linux docs are a good source of information on the specifics.  
  
shutdown:  
https://man7.org/linux/man-pages/man2/shutdown.2.html  
  
close:  
https://man7.org/linux/man-pages/man2/close.2.html  
  
You can think of `shutdown(read)` as artifically inserting an EOF marker in the read stream of the local socket. i.e. the next read will behave as if you had read a file in the EOF condition.  
  
You can think of `shutdown(write)` as causing the underlying stack to send an EOF marker to the remote socket, so that his next read will behave as if he was reading a file in the EOF condition. It will also cause all subsequent writes on the local socket to fail.  
  
`shutdown(both)` does both.  
  
`close(fd)` tears down the socket so that it no longer exists from the program's point of view. However doing this without a `shutdown` will cause the OS to let the socket live on for another 5 minutes. This is to ensure that if another local socket is opened with the same port number, traffic destined for the old socket is not confused with the new socket. TCP/IP allows for packets to "live" for up to 255 seconds in the internet. Remember that TCP/IP is a 60-year-old derivative of DARPANET which was designed to withstand a global nuclear war, allowing packets to find any route available to their destination - including causing telephone modems to have to connect-dial.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-07-02 15:22:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-653071285  

The fastest way to exit the program is: call `io_context::stop`, join any threads that you created, then allow `main` to exit.

---

## Comment 5

> Username: ashjas  
> Created at: 2020-07-09 09:57:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-656033050  

@vinniefalco  @madmongo1   
Thank you for your help and support.  
  
@vinniefalco I can understand, ```io_context::stop``` would be beneficial where there would be a ```io_context.run()``` loop.But as of now, we are facing this issue with the sync flow of beast apis, where we dont use the run() loop. So we do a shutdown and close on the socket. Correct me if i am wrong in this understanding.  
  
I am experiencing a strange problem on windows when i issue ```socket.shutdown()``` and ```socket.close()```. Let me explain:  
  
Our application has few threads that block on a read() call and only return after the data is recieved on the socket.[This wait can be from 0-20 seconds for a poll(implemented through REST communication) ].  
Normally, these secondary threads do not cause any unresponsiveness on the main UI thread, and this 20 sec blocking is absolutley normal, since this is the way we manage talking to some legacy component in our product suite.  
  
But,at application exit, we need to force close so that these threads get cancelled, and the application can close as soon as possible. To achieve this,at exit, we do a ```socket.shutdown()``` and ```socket.close()``` on all opened sockets on these threads, and wait for the threads to return.  
During this shutdown process, 3 out of these 4 threads get forcefully cancelled, and end up in the following exception: ```A blocking operation was interrupted by a call to WSACancelBlockingCall```.  
  
But one thread does not throw this exception, and continues to wait till the socket gets data, even though same set of shutdown and close calls did happen for that thread aswell. So the main thread keeps on waiting for this thread to get finished, causing the application to get unresponsive on exit for X seconds which can be anywhere between 0-20 seconds.  
  
We observer this behaviour only with our product application.  
  
**So is there any reason you could think of why a ```socket.shutdown()``` and ```socket.close()``` call won't end up in a ```WSACancelBlockingCall``` exception on windows?**   
  
Our product application behaves as expected on linux. i.e all the blocking threads get closed immediately, though the exception message is different.  
instead of ```WSACancelBlockingCall``` and errorcode: 10004  
i get: ```end of stream``` as reason of exit with errorcode: 1 on linux.  
  
We are on windows 10.  
  
Thanks for the help.  
  
PS: I could not reproduce this issue with a standalone unit test application that work with the same REST calls that block our product application. With the unit test application using the same beast sync apis, both on windows and linux, i get the same expected behaviour, which is the threads get immediately cancelled reporting the ```WSACancelBlockingCall``` exception on windows, and  ```end of stream``` on linux.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-07-09 10:38:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-656051560  

If you're calling shutdown() or close() from a different thread to the one that issued the read(), all bets are off. That's UB.  
  
If you need early cancelation, you will need to either:  
a) use an async read, or  
b) use native OS calls.

---

## Comment 7

> Username: ashjas  
> Created at: 2020-07-14 15:26:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-658245794  

@madmongo1 Thanks for your suggestion and insights, i was able to solve the early cancellation problem using async_read.  
  
But i am facing another problem.  
  
I have a wrapper class that holds a BeastSession class having io_context and other core implementation of sync and async interfaces.  
There is a point in the application when a shared_ptr to this wrapper class is reset(). Before this reset() happens, the implementation  
ensures that all pending tasks are either fully completed, or cancelled due to the early cancellation that we talked of in this discussion.  
reset() is only called on this shared_ptr when the io_context.stopped() returns true.  
But even then, i get this segfault.  
If i do not destruct this shared_ptr explictly, and let the application exit without it being destructed, the crash is not observed.  
```  
Program terminated with signal 11, Segmentation fault.  
#0  0x00007f0ac9d7c597 in boost::asio::detail::epoll_reactor::cancel_timer<boost::asio::detail::chrono_time_traits<std::chrono::_V2::steady_clock, boost::asio::wait_traits<std::chrono::_V2::steady_clock> > > ()  
   from myrestlib.so  
(gdb) bt  
#0  0x00007f0ac9d7c597 in boost::asio::detail::epoll_reactor::cancel_timer<boost::asio::detail::chrono_time_traits<std::chrono::_V2::steady_clock, boost::asio::wait_traits<std::chrono::_V2::steady_clock> > > ()  
   from myrestlib.so  
#1  0x00007f0ac9d7c7ab in boost::asio::detail::io_object_impl<boost::asio::detail::deadline_timer_service<boost::asio::detail::chrono_time_traits<std::chrono::_V2::steady_clock, boost::asio::wait_traits<std::chrono::_V2::steady_clock> > >, boost::asio::executor>::~io_object_impl () from myrestlib.so  
#2  0x0000000000000000 in ?? ()  
(gdb)   
```  
Since there isnt much of documentation within the epoll_reactor impl under asio, i cant seem to figure out what could be causing this.  
  
Can you point out some reasoning behind this crash?  
  
Thanks.

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-07-14 17:36:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-658314663  

Ok, I’m going to need to be able to replicate this locally before I can comment.  
  
Are you able to post a link to a 1-file complete program that exhibits this?

---

## Comment 9

> Username: ashjas  
> Created at: 2020-07-16 10:30:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-659324048  

@madmongo1   
  
Ok, i will try to get back on this if i can recreate this issue in a standalone sample.  
  
Thanks for all the support.

---

## Comment 10

> Username: stale[bot]  
> Created at: 2020-08-16 07:19:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-674490934  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 11

> Username: stale[bot]  
> Created at: 2020-08-23 09:00:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2004#issuecomment-678748639  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
