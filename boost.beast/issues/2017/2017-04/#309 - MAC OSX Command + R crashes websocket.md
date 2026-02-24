# #309 - MAC OSX Command + R crashes websocket [Closed]

> Username: goflatworld  
> Created at: 2017-04-20 05:16:55 UTC  
> Updated at: 2017-06-09 01:48:14 UTC  
> Closed at: 2017-06-09 01:48:14 UTC  
> Url: https://github.com/boostorg/beast/issues/309  

Hi Vinnie,  
  
I use beast to handle websocket. One issue I noticed is that in the Web browser, on MAC OSX if I press "Command" + R key, after multiple tries (normally 5 or 6 times). My service quit without any errors in my log. It does generate a core file. Please see attached core file  
[_sbin_stingray.0.crash.zip](https://github.com/vinniefalco/Beast/files/940483/_sbin_stingray.0.crash.zip)  
  
Also, I used "strace -p xxx" to attach my serive PID. When it crashes, it is something like below: The last few lines point to "write(2, "stingray: /usr/include/beast/web"..., 988) = -1 EPIPE (Broken pipe)"  
  
sendmsg(35, {msg_name(0)=NULL, msg_iov(1)=[{"\27\3\3\0000vB\255\254S\276\265\232a\254<6\260\365p`hn)z\235\17~IJ{\215"..., 53}], msg_controllen=0, msg_flags=0}, MSG_NOSIGNAL) = 53  
epoll_wait(8, [], 128, 0)               = 0  
sendmsg(35, {msg_name(0)=NULL, msg_iov(1)=[{"\27\3\3\00002,\177\352\213\204 \357\206\7\366\373ec3\276C8v\316r\6\252\354\207y\373"..., 53}], msg_controllen=0, msg_flags=0}, MSG_NOSIGNAL) = 53  
epoll_wait(8, [], 128, 0)               = 0  
sendmsg(35, {msg_name(0)=NULL, msg_iov(1)=[{"\27\3\3\0000u\257\rn\27212\215\376y\21L\330\362\272C,>\7\312\321r?\251\3647\34"..., 53}], msg_controllen=0, msg_flags=0}, MSG_NOSIGNAL) = 53  
epoll_wait(8, [], 128, 0)               = 0  
write(2, "stingray: /usr/include/beast/web"..., 988) = -1 EPIPE (Broken pipe)  
--- SIGPIPE {si_signo=SIGPIPE, si_code=SI_USER, si_pid=1221, si_uid=0} ---  
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5c17275000  
rt_sigprocmask(SIG_UNBLOCK, [ABRT], NULL, 8) = 0  
tgkill(1221, 1221, SIGABRT)             = 0  
--- SIGABRT {si_signo=SIGABRT, si_code=SI_TKILL, si_pid=1221, si_uid=0} ---  
+++ killed by SIGABRT (core dumped) +++  
  
If you could look into this, it would be much appreciated.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-20 16:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-295797690  

I'm not really sure what to do about that core dump. You say that when you refresh the browser, then your "service" quits. What is your service? Do you mean that you wrote a WebSocket server using Beast which crashes when you connect using a browser (Javascript client app)? Where is the source code for the program that crashes?

---

## Comment 2

> Username: goflatworld  
> Created at: 2017-04-21 02:52:29 UTC  
> Updated at: 2017-04-21 02:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296029074  

Hi Vinnie,  
  
Thanks for the update.  
  
Yes. My service is written in Beast/Boost. For websocket, it is Beast. The client is javascript.  
  
It works fine if my keyboard input is normal character or some other control key. It crashes if I type "COMMAND" + R combination key. --- The COMMAND is MAC OSX key.  
  
The code URL is below:    
https://github.com/goflatworld/stingray  
  
The key file is session.cpp. The code which causes the crash should be in this file. If it isn't, it might be channel.cpp.  
  
Thanks  
  
George

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-21 16:32:39 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296239602  

I had a look over your code, nothing jumps out at me. When you press CMD+R it means "Refresh." Usually browsers will close their connections and re-establish them upon a refresh. So I would look carefully at your completion handlers and make sure you are correctly handling the destruction of the socket and the objects which contain it. Getting destruction right usually takes some analysis and trial and error, it can be tricky.

---

## Comment 4

> Username: goflatworld  
> Created at: 2017-04-21 22:14:36 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296317754  

Thanks Vinnie for looking into it. I'll look into your suggestion of getting destruction right.  
  
More info: When I press CMD+R, my server get a "short read" error and error handling close the socket. When my server don't have a "short read" error, the service crashes. Does this ring any bell?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-04-21 22:30:35 UTC  
> Updated at: 2017-04-21 23:58:33 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296320131  

That sounds like an OpenSSL error. I think you aren't handling the shutdown and destruction of the socket correctly. See: https://github.com/vinniefalco/Beast/issues/38

---

## Comment 6

> Username: goflatworld  
> Created at: 2017-04-21 22:43:16 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296321891  

Thanks. I'll look into it.

---

## Comment 7

> Username: goflatworld  
> Created at: 2017-04-24 06:00:02 UTC  
> Updated at: 2017-04-24 06:02:44 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296531378  

Hi Vinnie,  
  
Thanks for that. I have modified my code to handle unexpectedly closed SSL socket. It closes normal socket (not websocket) properly. However I can not close Beast websocket. Can you please let me know how can I close beast websocket in async mode?  
  
The code I used to close the socket is as below:  
  
```  
wssocket_.next_layer().async_shutdown(strand_.wrap(boost::bind(&session::handle_shutdown,shared_from_this(), boost::asio::placeholders::error)));  
  
 void session::handle_shutdown(const boost::system::error_code& error)  
            {  
              if (!error)  
                    if (wssocket_.lowest_layer().is_open())  
                        wssocket_.lowest_layer().close();   
            }  
```  
Above code works for normal socket but appears doesn't work for Websocket.   
  
I checked your async websocket example, but I'm not sure how to use it in my code if  a websocket is closed unexpectedly.  
  
Thanks  
  
George

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-04-24 18:48:43 UTC  
> Updated at: 2017-04-24 18:49:33 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296787459  

For Beast SSL websockets, this is "Close the Websocket Connection" process described here https://tools.ietf.org/html/rfc6455#section-7.1.1  
  
1. `websocket::stream::async_close(...)`  
2. Repeat `websocket::stream::async_read(...)` until `websocket::error::closed` is returned  
3. `websocket::stream::next_layer().async_shutdown(...)`  
4. `~websocket::stream()`  
  
If any completion handler receives an error (other than `error::closed` above), jump to step 4.

---

## Comment 9

> Username: goflatworld  
> Created at: 2017-04-25 00:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-296856487  

Thanks Vinnie.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-04-28 00:43:33 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-297877070  

It looks like this issue is resolved. If it is still a problem feel free to reopen it. Or make a new issue, whatever is most appropriate for you, thanks!

---

## Comment 11

> Username: goflatworld  
> Created at: 2017-04-30 03:54:49 UTC  
> Updated at: 2017-04-30 03:55:14 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298209511  

Hi Vinnie,  
  
When a websocket client got disconnected without any notification. If I use   
 ```wssocket_.close(beast::websocket::close_code::normal);```  My own service also stops.  
  
If I use ``` wssocket_.async_close(beast::websocket::close_code::none, strand_.wrap(boost::bind(&session::http_handle_shutdown,shared_from_this(),  
                                            boost::asio::placeholders::error)));```  
I can not close the underlying TCP session, the server stuck in "CLOSE_WAIT" status and the client stuck in "FIN_WAIT_2" status.  
  
I'm not sure how to use step 4.  
  
Is it possible to have some example to demonstrate how to shutdown TCP connection from server side?  
  
Thanks  
  
George

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-04-30 05:40:36 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298212792  

Are you calling `async_close` from the same strand, or are you calling it from a different thread? When you call `async_close`, do you have any other call to `async_write` or `async_ping` pending?

---

## Comment 13

> Username: goflatworld  
> Created at: 2017-04-30 23:30:45 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298264412  

The code is based on the boost async chat_server. Since it is a single thread app. I think I call it from the same thread?  
  
When I call async_close, I don't have async_write. I don't use async_ping and I thought that beast does ping my me automatically?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-04-30 23:56:56 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298265620  

Beast doesn't send pings automatically, but it responds to them automatically.

---

## Comment 15

> Username: goflatworld  
> Created at: 2017-05-01 00:59:24 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298269209  

I put the async_close in the error handling section. e.g. if beast async_write error or if beast async_read error, async_close will be called. Should it close the connection?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-05-01 01:05:02 UTC  
> Updated at: 2017-05-01 01:05:24 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298269593  

When an error occurs on a connection, you should not call any more asynchronous operations. Instead, you should simply close the underlying socket. For example by calling `next_layer()::close()` on the websocket stream, or by destroying the websocket stream object.   
  
`websocket::stream::async_close` is for performing a _WebSocket closing handshake_. Its a structured, graceful close that takes place when both sides are connected.  You can read about it here:  
https://tools.ietf.org/html/rfc6455#section-7.1.2  
  
I strongly advise familiarizing yourself with the entire WebSocket document (**rfc6455**).

---

## Comment 17

> Username: goflatworld  
> Created at: 2017-05-01 01:13:56 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298270203  

Thanks Vinnie for the great help.  
  
That fixed the issue!   
  
Cheers  
  
George

---

## Comment 18

> Username: goflatworld  
> Created at: 2017-05-01 05:57:51 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298290816  

Hi Vinnie,  
  
My understanding is that if a client close the websocket connection, from the server side, there is NO code (e.g. async_close/close) required. Beast Websocket will handle the close automatically for me. Is this correct?  Thanks.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2017-05-01 06:58:14 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298296032  

Yes that is correct. For this to work, you have to have an active read.

---

## Comment 20

> Username: goflatworld  
> Created at: 2017-05-02 00:52:40 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298470672  

The websocket still crashes if I refresh the web page multiple times. It happens not only on MAC, it also happens on Windows.   
  
I think my code has covered websocket read error and write error, I could not find out anything wrong with the code now. Is it possible that before the "refresh", websocket send ping and immdately the connection got disconnected via "refresh" but beast websocket tries to reply the ping and got error?

---

## Comment 21

> Username: vinniefalco  
> Created at: 2017-05-02 01:12:15 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298473320  

If you're crashing on a refresh, you must be breaking one of the asynchronous invariants. Beast only replies to a ping if you have an asynchronous read active. If you have an asynchronous read active, you can't destroy the steam. See the _Remarks_ section of the stream documentation:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream.html

---

## Comment 22

> Username: goflatworld  
> Created at: 2017-05-02 01:18:05 UTC  
> Updated at: 2017-05-02 01:18:22 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298474027  

Thanks. I'll check the code again.  
  
More test shows that if I don't send data to the websocket, refresh doesn't crash. It only crashes when I send lots of data to the websocket and then "refresh".  Does this  ring any bell?

---

## Comment 23

> Username: harrishancock  
> Created at: 2017-05-02 01:20:27 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298474309  

@goflatworld is it possible to get a stack trace from the crash, or better yet to run the service under valgrind (or Dr. Memory on Windows)? That might give you a some clues as to where the problem lies.

---

## Comment 24

> Username: goflatworld  
> Created at: 2017-05-02 01:22:09 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298474509  

Thanks I'll look into this.

---

## Comment 25

> Username: goflatworld  
> Created at: 2017-05-02 01:48:13 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298477619  

I run "strace" and compiled the app with debug. When the application crashes, below is the strace output.  
  
```write(2, "stingray: /usr/include/beast/web"..., 988stingray: /usr/include/beast/websocket/impl/read_frame_op.ipp:454: void beast::websocket::stream<NextLayer>::read_frame_op<DynamicBuffer, Handler>::operator()(beast::error_code, std::size_t, bool) [with DynamicBuffer = boost::asio::basic_streambuf<>; Handler = beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> > >::read_op<boost::asio::basic_streambuf<>, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, boost::_bi::bind_t<void, boost::_mfi::mf1<void, websocket::applications::chat::session, const boost::system::error_code&>, boost::_bi::list2<boost::_bi::value<std::shared_ptr<websocket::applications::chat::session> >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running> >; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `! d.ws.wr_block_' failed.  
) = 988  
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f4c6b606000  
rt_sigprocmask(SIG_UNBLOCK, [ABRT], NULL, 8) = 0  
tgkill(8620, 8620, SIGABRT)             = 0 ```  
  
I tried run my app with valgrind but it even doesn't start my app.   
  
```==8505== Memcheck, a memory error detector  
==8505== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.  
==8505== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info  
==8505== Command: /sbin/stingray 443  
==8505==   
==8505==   
==8505== HEAP SUMMARY:  
==8505==     in use at exit: 172,946 bytes in 3,297 blocks  
==8505==   total heap usage: 3,389 allocs, 92 frees, 194,066 bytes allocated  
==8505==   
==8505== LEAK SUMMARY:  
==8505==    definitely lost: 0 bytes in 0 blocks  
==8505==    indirectly lost: 0 bytes in 0 blocks  
==8505==      possibly lost: 0 bytes in 0 blocks  
==8505==    still reachable: 172,946 bytes in 3,297 blocks  
==8505==         suppressed: 0 bytes in 0 blocks  
==8505== Reachable blocks (those to which a pointer was found) are not shown.  
==8505== To see them, rerun with: --leak-check=full --show-leak-kinds=all  
==8505==   
==8505== For counts of detected and suppressed errors, rerun with: -v  
==8505== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
root@ip-172-31-42-174:/home/ubuntu/oob# ==8506==   
==8506== HEAP SUMMARY:  
==8506==     in use at exit: 172,946 bytes in 3,297 blocks  
==8506==   total heap usage: 3,389 allocs, 92 frees, 194,066 bytes allocated  
==8506==   
==8506== LEAK SUMMARY:  
==8506==    definitely lost: 0 bytes in 0 blocks  
==8506==    indirectly lost: 0 bytes in 0 blocks  
==8506==      possibly lost: 0 bytes in 0 blocks  
==8506==    still reachable: 172,946 bytes in 3,297 blocks  
==8506==         suppressed: 0 bytes in 0 blocks  
==8506== Reachable blocks (those to which a pointer was found) are not shown.  
==8506== To see them, rerun with: --leak-check=full --show-leak-kinds=all  
==8506==   
==8506== For counts of detected and suppressed errors, rerun with: -v  
==8506== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
vex amd64->IR: unhandled instruction bytes: 0xF 0xC7 0xF0 0x89 0x6 0xF 0x42 0xC1  
vex amd64->IR:   REX=0 REX.W=0 REX.R=0 REX.X=0 REX.B=0  
vex amd64->IR:   VEX=0 VEX.L=0 VEX.nVVVV=0x0 ESC=0F  
vex amd64->IR:   PFX.66=0 PFX.F2=0 PFX.F3=0  
==8507== valgrind: Unrecognised instruction at address 0x5cddb15.  
==8507==    at 0x5CDDB15: ??? (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.21)  
==8507==    by 0x5CDDCB1: std::random_device::_M_getval() (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.21)  
==8507==    by 0x45C77D: std::random_device::operator()() (random.h:1612)  
==8507==    by 0x4694C9: beast::websocket::detail::maskgen_t<std::linear_congruential_engine<unsigned long, 48271ul, 0ul, 2147483647ul> >::rekey() (mask.hpp:65)  
==8507==    by 0x4648A8: beast::websocket::detail::maskgen_t<std::linear_congruential_engine<unsigned long, 48271ul, 0ul, 2147483647ul> >::maskgen_t() (mask.hpp:45)  
==8507==    by 0x45FDB6: beast::websocket::detail::stream_base::stream_base() (stream_base.hpp:132)  
==8507==    by 0x46565C: beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > > >::stream<boost::asio::io_service&, boost::asio::ssl::context&>(boost::asio::io_service&, boost::asio::ssl::context&) (stream.ipp:45)  
==8507==    by 0x42D6E6: websocket::applications::chat::session::session(boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&) (session.cpp:12)  
==8507==    by 0x42C859: _ZN9__gnu_cxx13new_allocatorIN9websocket12applications4chat7sessionEE9constructIS4_IRN5boost4asio10io_serviceERNS8_3ssl7contextERNS3_7channelEEEEvPT_DpOT0_ (in /sbin/stingray)  
==8507==    by 0x42C5C1: _ZNSt16allocator_traitsISaIN9websocket12applications4chat7sessionEEE9constructIS3_IRN5boost4asio10io_serviceERNS8_3ssl7contextERNS2_7channelEEEEvRS4_PT_DpOT0_ (alloc_traits.h:530)  
==8507==    by 0x42C0E8: std::_Sp_counted_ptr_inplace<websocket::applications::chat::session, std::allocator<websocket::applications::chat::session>, (__gnu_cxx::_Lock_policy)2>::_Sp_counted_ptr_inplace<boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&>(std::allocator<websocket::applications::chat::session>, boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&) (shared_ptr_base.h:522)  
==8507==    by 0x42BA66: std::__shared_count<(__gnu_cxx::_Lock_policy)2>::__shared_count<websocket::applications::chat::session, std::allocator<websocket::applications::chat::session>, boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&>(std::_Sp_make_shared_tag, websocket::applications::chat::session*, std::allocator<websocket::applications::chat::session> const&, boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&) (shared_ptr_base.h:617)  
==8507== Your program just tried to execute an instruction that Valgrind  
==8507== did not recognise.  There are two possible reasons for this.  
==8507== 1. Your program has a bug and erroneously jumped to a non-code  
==8507==    location.  If you are running Memcheck and you just saw a  
==8507==    warning about a bad jump, it's probably your program's fault.  
==8507== 2. The instruction is legitimate but Valgrind doesn't handle it,  
==8507==    i.e. it's Valgrind's fault.  If you think this is the case or  
==8507==    you are not sure, please let us know and we'll try to fix it.  
==8507== Either way, Valgrind will now raise a SIGILL signal which will  
==8507== probably kill your program.  
==8507==   
==8507== Process terminating with default action of signal 4 (SIGILL)  
==8507==  Illegal opcode at address 0x5CDDB15  
==8507==    at 0x5CDDB15: ??? (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.21)  
==8507==    by 0x5CDDCB1: std::random_device::_M_getval() (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.21)  
==8507==    by 0x45C77D: std::random_device::operator()() (random.h:1612)  
==8507==    by 0x4694C9: beast::websocket::detail::maskgen_t<std::linear_congruential_engine<unsigned long, 48271ul, 0ul, 2147483647ul> >::rekey() (mask.hpp:65)  
==8507==    by 0x4648A8: beast::websocket::detail::maskgen_t<std::linear_congruential_engine<unsigned long, 48271ul, 0ul, 2147483647ul> >::maskgen_t() (mask.hpp:45)  
==8507==    by 0x45FDB6: beast::websocket::detail::stream_base::stream_base() (stream_base.hpp:132)  
==8507==    by 0x46565C: beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > > >::stream<boost::asio::io_service&, boost::asio::ssl::context&>(boost::asio::io_service&, boost::asio::ssl::context&) (stream.ipp:45)  
==8507==    by 0x42D6E6: websocket::applications::chat::session::session(boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&) (session.cpp:12)  
==8507==    by 0x42C859: _ZN9__gnu_cxx13new_allocatorIN9websocket12applications4chat7sessionEE9constructIS4_IRN5boost4asio10io_serviceERNS8_3ssl7contextERNS3_7channelEEEEvPT_DpOT0_ (in /sbin/stingray)  
==8507==    by 0x42C5C1: _ZNSt16allocator_traitsISaIN9websocket12applications4chat7sessionEEE9constructIS3_IRN5boost4asio10io_serviceERNS8_3ssl7contextERNS2_7channelEEEEvRS4_PT_DpOT0_ (alloc_traits.h:530)  
==8507==    by 0x42C0E8: std::_Sp_counted_ptr_inplace<websocket::applications::chat::session, std::allocator<websocket::applications::chat::session>, (__gnu_cxx::_Lock_policy)2>::_Sp_counted_ptr_inplace<boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&>(std::allocator<websocket::applications::chat::session>, boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&) (shared_ptr_base.h:522)  
==8507==    by 0x42BA66: std::__shared_count<(__gnu_cxx::_Lock_policy)2>::__shared_count<websocket::applications::chat::session, std::allocator<websocket::applications::chat::session>, boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&>(std::_Sp_make_shared_tag, websocket::applications::chat::session*, std::allocator<websocket::applications::chat::session> const&, boost::asio::io_service&, boost::asio::ssl::context&, websocket::applications::chat::channel&) (shared_ptr_base.h:617)  
==8507==   
==8507== HEAP SUMMARY:  
==8507==     in use at exit: 222,457 bytes in 3,882 blocks  
==8507==   total heap usage: 4,691 allocs, 809 frees, 419,777 bytes allocated  
==8507==   
==8507== 56 bytes in 1 blocks are possibly lost in loss record 605 of 754  
==8507==    at 0x4C2DB8F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8507==    by 0x65E9A77: ??? (in /usr/lib/x86_64-linux-gnu/libmysqlclient.so.20.3.4)  
==8507==    by 0x65E8AEC: ??? (in /usr/lib/x86_64-linux-gnu/libmysqlclient.so.20.3.4)  
==8507==    by 0x65AC784: mysql_server_init (in /usr/lib/x86_64-linux-gnu/libmysqlclient.so.20.3.4)  
==8507==    by 0x4F19499: sql::mysql::NativeAPI::LibmysqlStaticProxy::library_init(int, char**, char**) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F19139: sql::mysql::NativeAPI::LibmysqlStaticProxy::LibmysqlStaticProxy() (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F19EC4: sql::mysql::util::Singleton<sql::mysql::NativeAPI::LibmysqlStaticProxy>::theInstance() (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F190AE: sql::mysql::NativeAPI::getCApiHandle(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F1A7AA: sql::mysql::NativeAPI::MySQL_NativeDriverWrapper::MySQL_NativeDriverWrapper(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F1A730: sql::mysql::NativeAPI::createNativeDriverWrapper(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4ECE66A: sql::mysql::MySQL_Driver::MySQL_Driver(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4ECE3BA: sql::mysql::get_driver_instance_by_name(char const*) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==   
==8507== 176 bytes in 1 blocks are possibly lost in loss record 654 of 754  
==8507==    at 0x4C2DB8F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8507==    by 0x65E9A77: ??? (in /usr/lib/x86_64-linux-gnu/libmysqlclient.so.20.3.4)  
==8507==    by 0x65E7CA7: ??? (in /usr/lib/x86_64-linux-gnu/libmysqlclient.so.20.3.4)  
==8507==    by 0x65BE25B: ??? (in /usr/lib/x86_64-linux-gnu/libmysqlclient.so.20.3.4)  
==8507==    by 0x65AC78B: mysql_server_init (in /usr/lib/x86_64-linux-gnu/libmysqlclient.so.20.3.4)  
==8507==    by 0x4F19499: sql::mysql::NativeAPI::LibmysqlStaticProxy::library_init(int, char**, char**) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F19139: sql::mysql::NativeAPI::LibmysqlStaticProxy::LibmysqlStaticProxy() (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F19EC4: sql::mysql::util::Singleton<sql::mysql::NativeAPI::LibmysqlStaticProxy>::theInstance() (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F190AE: sql::mysql::NativeAPI::getCApiHandle(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F1A7AA: sql::mysql::NativeAPI::MySQL_NativeDriverWrapper::MySQL_NativeDriverWrapper(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4F1A730: sql::mysql::NativeAPI::createNativeDriverWrapper(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==    by 0x4ECE66A: sql::mysql::MySQL_Driver::MySQL_Driver(sql::SQLString const&) (in /usr/lib/libmysqlcppconn.so.7.1.1.7)  
==8507==   
==8507== LEAK SUMMARY:  
==8507==    definitely lost: 0 bytes in 0 blocks  
==8507==    indirectly lost: 0 bytes in 0 blocks  
==8507==      possibly lost: 232 bytes in 2 blocks  
==8507==    still reachable: 222,225 bytes in 3,880 blocks  
==8507==         suppressed: 0 bytes in 0 blocks  
==8507== Reachable blocks (those to which a pointer was found) are not shown.  
==8507== To see them, rerun with: --leak-check=full --show-leak-kinds=all  
==8507==   
==8507== For counts of detected and suppressed errors, rerun with: -v  
==8507== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)```

---

## Comment 26

> Username: harrishancock  
> Created at: 2017-05-02 01:59:48 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298479024  

The ```Assertion `! d.ws.wr_block_' failed.``` from the strace output looks like a faint clue.  
  
A better stack trace might come from gdb -- have you given that a shot? I.e., `gdb stingray`, then in the gdb console, `run` and `bt` after it crashes.  
  
It looks like your valgrind doesn't support the RDRAND instruction (google "rdrand valgrind" and you'll see similar problem reports). Is it possible to upgrade it, perhaps from a PPA?  
  
If your service source is still available publicly, I'd be happy to look at it.

---

## Comment 27

> Username: goflatworld  
> Created at: 2017-05-02 02:34:25 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298483109  

Thanks. I'll look into it.   
  
Please see source code here: https://github.com/goflatworld/stingray

---

## Comment 28

> Username: harrishancock  
> Created at: 2017-05-02 02:37:30 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298483458  

I see it, reading now.

---

## Comment 29

> Username: goflatworld  
> Created at: 2017-05-02 02:48:22 UTC  
> Updated at: 2017-05-02 02:48:40 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298484660  

gdb reports "No stack".  
  
```  
gdb --args stingray 443  
  
GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.04) 7.11.1  
Copyright (C) 2016 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"  
and "show warranty" for details.  
This GDB was configured as "x86_64-linux-gnu".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<http://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
<http://www.gnu.org/software/gdb/documentation/>.  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from stingray...done.  
(gdb) run  
Starting program: /home/ubuntu/oob/stingray 443  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
[Inferior 1 (process 9131) exited normally]  
(gdb) stingray: /usr/include/beast/websocket/impl/read_frame_op.ipp:454: void beast::websocket::stream<NextLayer>::read_frame_op<DynamicBuffer, Handler>::operator()(beast::error_code, std::size_t, bool) [with DynamicBuffer = boost::asio::basic_streambuf<>; Handler = beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> > >::read_op<boost::asio::basic_streambuf<>, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, boost::_bi::bind_t<void, boost::_mfi::mf1<void, websocket::applications::chat::session, const boost::system::error_code&>, boost::_bi::list2<boost::_bi::value<std::shared_ptr<websocket::applications::chat::session> >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running> >; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `! d.ws.wr_block_' failed.  
  
(gdb) bt  
No stack.  
(gdb) ```

---

## Comment 30

> Username: harrishancock  
> Created at: 2017-05-02 03:00:27 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298485999  

Hmm, maybe gdb doesn't break on assertion failures by default. Try:  
```  
$ gdb stingray  
> break abort  
> run  
> bt  
```

---

## Comment 31

> Username: goflatworld  
> Created at: 2017-05-02 03:05:25 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298486504  

Same thing no stack.   
  
I compile it with -o0 -g. Is this good enough?

---

## Comment 32

> Username: vinniefalco  
> Created at: 2017-05-02 03:40:14 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298490067  

`assert(! d.ws.wr_block_)` means you are trying to have two active asynchronous writes at the same time

---

## Comment 33

> Username: vinniefalco  
> Created at: 2017-05-02 03:41:24 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298490190  

If your program does not run with valgrind, I strongly recommend starting over with a simple `main`, and bringing back pieces of your program a little bit at a time, making sure that it runs under valgrind at every step of the way. This is how I developed Beast, valgrind has caught so many bugs that I have simply lost count! Its an invaluable tool, especially for asynchronous programs. My only regret is that it is not available in a realistic way on Windows, so I have to launch a time-consuming Travis CI build when I want to see results.

---

## Comment 34

> Username: goflatworld  
> Created at: 2017-05-02 03:51:48 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298491204  

Thanks. I'll try valgrind.

---

## Comment 35

> Username: harrishancock  
> Created at: 2017-05-02 04:15:01 UTC  
> Updated at: 2017-05-02 04:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298493422  

I second what @vinniefalco said about valgrind -- I would bet that will get you useful information quickest.  
  
A stack trace might still be useful -- if gdb won't do it, Boost.Stacktrace appears to offer a cookbook solution ([link](http://boostorg.github.io/stacktrace/boost_stacktrace/getting_started.html#boost_stacktrace.getting_started.better_asserts)) to getting the stack trace from a `BOOST_ASSERT` if you're feeling motivated. It's available on GitHub, not yet in a Boost release.  
  
Another angle of attack would be to get the asynchronous call graph using [Asio's handler tracking feature](http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/overview/core/handler_tracking.html) That would help you see the chains of handlers that are running on your event loop, and ultimately shed some light into why a write is being initiated at the wrong time. It's tedious (the GraphViz tool might help), but it has helped me diagnose problems like this before.  
  
One more idea: You can figure out which operation is directly causing the assertion by modulating your handler types. You see how the assertion prints out your handler type? This thing here:  
  
```c++  
boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, boost::_bi::bind_t<void, boost::_mfi::mf1<void, websocket::applications::chat::session, const boost::system::error_code&>, boost::_bi::list2<boost::_bi::value<std::shared_ptr<websocket::applications::chat::session> >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running>  
```  
  
That type is generated from one of your `strand_.wrap(boost::bind(...))` expressions, specifically one that binds only a session pointer and an error/_1 placeholder. If you go through session.cpp and change each of those into  
```c++  
strand_.wrap([self = shared_from_this()](const error_code& ec) { self->handle_xxx(ec); })  
```  
and observe how that type printed by the assertion changes, you should be able to figure out which operation is triggering the assertion.

---

## Comment 36

> Username: goflatworld  
> Created at: 2017-05-02 04:21:29 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-298494021  

Thanks. I'll try the suggestions.

---

## Comment 37

> Username: goflatworld  
> Created at: 2017-05-27 10:45:18 UTC  
> Updated at: 2017-05-27 10:45:36 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304444328  

Hi Vinnie,  
  
I have done below to stop my app from crash.   
  
I modified below file:  
/usr/include/beast/websocket/impl/read_frame_op.ipp  
  
 case do_close:  
                d.state = do_close + 1;  
                d.ws.wr_close_ = true;  
                return;      ------ > Added this line to ignore the close.  
  
The reason is that we have identified that it was the async_read caused the crashes. However, I can guarantee you that I only have one read during the crashes. I also do not close any websocket from server side unless if there is a read/write error. I don't know why CMD+R causes beast run to "do_close" section even I did not ask to close the socket. By simply disabling it. My app doesn't crash anymore.  
  
I'm not sure the side effect of this yet, can you please help to analysis it?  
  
Thanks  
  
George

---

## Comment 38

> Username: vinniefalco  
> Created at: 2017-05-27 14:04:39 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304453400  

>I modified below file:  
>/usr/include/beast/websocket/impl/read_frame_op.ipp  
  
What version of Beast are you using?

---

## Comment 39

> Username: goflatworld  
> Created at: 2017-05-28 04:31:18 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304492163  

#define BEAST_VERSION_STRING "1.0.0-b16"

---

## Comment 40

> Username: vinniefalco  
> Created at: 2017-05-28 05:33:37 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304494090  

Please try the latest version (v44).

---

## Comment 41

> Username: goflatworld  
> Created at: 2017-05-29 00:00:32 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304548594  

I tried v44. With V44, my client and server doesn't communicate properly via SSL. SSL handshake OK, socket also accepted, but seems can not transmit data after that. I then rollback to the old beast, it works. I need more time to find out why my code doesn't work with V44. I use socket.next_layer to read data from non-websocket client.

---

## Comment 42

> Username: goflatworld  
> Created at: 2017-05-29 10:10:39 UTC  
> Updated at: 2017-05-29 10:37:27 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304625619  

More information about my app when run V44. SSL handshake OK, but Websocket appears having issue to accept my non-websocket connection (or maybe blocked somewhere?). But when the server tries to close the connection, websocket report that it accepted the socket (after the socket closure).   
  
The code is as below:  
  
```  
            void session::handle_handshake(const boost::system::error_code& error)  
            {  
                if (!error)  
                {  
                        log_msg_="SSL handshake with IP address " + ipaddress_ +" finished successfully.\n";  
                        save_log({log_msg_});  
  
                        wssocket_.async_accept(strand_.wrap(  
                            boost::bind(&session::http_handle_accept, shared_from_this(),  
                            boost::asio::placeholders::error)));  
                } else  
                {  
                    log_time();  
                    fprintf(logfile,"%s: SSL handshake with IP address %s failed.\n",logtime.c_str(), ipaddress_.c_str());  
                    fflush(logfile);  
                    wssocket_.next_layer().async_shutdown(strand_.wrap(boost::bind(&session::handle_shutdown,shared_from_this(),  
                                            boost::asio::placeholders::error)));  
                     
                }  
            }  
  
  void session::http_handle_accept(const boost::system::error_code& error)  
            {  
                if (!error)  
                {  
                    log_msg_="Successfully accepted Websocket connection from IP address " + ipaddress_+" after SSL handshake. Reading data now.\n";  
                    save_log({log_msg_});  
                    do_read_header(0);  
                }  
                else  
                {  
                   if (error != boost::asio::error::eof)  
                   {  
                            log_msg_="Successfully accepted normal socket connection from IP address " + ipaddress_+" after SSL handshake. Reading data now.\n";  
                            save_log({log_msg_});  
                            do_read_header(1);  
                                } else  
                   {  
                      log_msg_="Failed accepted the connection from IP address " + ipaddress_+"! But SSL handshake was OK.\n";  
                      save_log({log_msg_});  
                      wssocket_.next_layer().async_shutdown(  
                          strand_.wrap(boost::bind(&session::handle_shutdown,shared_from_this(),  
                              boost::asio::placeholders::error)));  
                   }  
   
                }  
            }  
```  
  
Does V44 change the way it handles "accept"?

---

## Comment 43

> Username: vinniefalco  
> Created at: 2017-05-29 15:30:53 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304687425  

The code you posted looks correct. I'm not sure why its not working for you. `accept` hasn't really changed in its behavior. It should work. There are both an OpenSSL WebSocket Client and an OpenSSL WebSocket Server examples programs which function correctly in the latest version of Beast:  
  
https://github.com/vinniefalco/Beast/blob/master/examples/ssl/websocket_ssl_example.cpp  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/ssl/websocket_async_ssl_echo_server.hpp

---

## Comment 44

> Username: goflatworld  
> Created at: 2017-05-29 23:03:02 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304741958  

Thanks Vinnie,  
  
I tried to use the SSL server example to see if it works in my env. However when I first compile it, I noticed that it doesn't have a main. So I checked the makefile, it appears that I need use the extras/beast/unit_test/main.cpp as main file? I then compile it. The server doesn't run as background application.  Can you please let me know how can I test the app?  
  
Thanks  
  
George

---

## Comment 45

> Username: vinniefalco  
> Created at: 2017-05-29 23:22:58 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304743277  

You might have to write your own main.cpp which creates the ssl server instance and leaves it running. That's why its in the test directory not the examples. Sorry about that!

---

## Comment 46

> Username: goflatworld  
> Created at: 2017-05-29 23:40:04 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-304744455  

Thanks Vinnie.

---

## Comment 47

> Username: vinniefalco  
> Created at: 2017-06-08 05:30:45 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-307002737  

Is this still an issue that requires attention?

---

## Comment 48

> Username: goflatworld  
> Created at: 2017-06-09 01:48:09 UTC  
> Url: https://github.com/boostorg/beast/issues/309#issuecomment-307273006  

Hi Vinnie,  
  
I'll try compile my app with latest Beast to see if it can accept normal socket or not.  
  
Please close this one.  
  
Thanks for your help.  
  
Best Regards  
  
George
