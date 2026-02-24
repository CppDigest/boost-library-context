# #147 - websocket server crashes [Closed]

> Username: goflatworld  
> Created at: 2016-10-18 12:47:49 UTC  
> Updated at: 2016-10-27 01:45:32 UTC  
> Closed at: 2016-10-20 22:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/147  

Thanks for your great help so far. I have added the websocket part to my server (not the one on github, the server is kind a commercial software, not really comfortable to put it on github ). During test, it crashes quite often. When the server crashes, the output is as below:  
  
server: /usr/include/beast/websocket/detail/invokable.hpp:138: void beast::websocket::detail::invokable::emplace(F&&) [with F = beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_frame_op >, beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_op, boost::bi::list2boost::_bi::value<std::shared_ptr<websocket::applications::chat::session >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running> > >]: Assertion `! base' failed.  
  
My server has two clients connect to it: one is a websocket client (I call it control), another one is normal socket client (I call it agent).  
  
I uses "control" client to control the "agent" (when I say control, I meant I type some command on the "control", it sends to server, server sends to "agent"). Then "agent" performs the required functions and sends result back.  
  
During test, if I send same command from "control" to "agent", it works sometimes but crashes another time.  
  
I also have a normal socket client to control the "agent". It worked fine. I want to replace the normal client with websocket client.  
  
Can you please give me some hints on where should I look into it?  
  
If code is required, can I send to you via email?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-18 12:49:20 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-254496739  

Please try this branch **invokable** and see if it solves your problem:  
https://github.com/vinniefalco/Beast/commits/invokable

---

## Comment 2

> Username: goflatworld  
> Created at: 2016-10-19 04:34:31 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-254708125  

Thanks for the quick response. I have tried the new version. So far, no crashes anymore. Thanks again.  
  
I'll update the case if it crashes again.

---

## Comment 3

> Username: goflatworld  
> Created at: 2016-10-20 03:01:45 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-254997054  

Today, it crashed twice.   
  
The first time, the error message is as below. The second time I didn't capture the error becuase my ssh session got disconnected.   
  
#   
  
server: /usr/include/beast/websocket/detail/invokable.hpp:138: void beast::websocket::detail::invokable::emplace(F&&) [with F = beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_frame_op<beast::prepared_buffers<beast::consuming_buffers<boost::asio::const_buffers_1, boost::asio::const_buffer> >, beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_op<boost::asio::const_buffers_1, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, boost::_bi::bind_t<void, boost::_mfi::mf1<void, websocket::applications::chat::session, const boost::system::error_code&>, boost::_bi::list2boost::_bi::value<std::shared_ptr<websocket::applications::chat::session >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running> > >]: Assertion `! base_' failed.  
  
#

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-10-20 04:21:31 UTC  
> Updated at: 2016-10-20 04:21:50 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255005564  

The purpose of `invokable` is to "park" a websocket operation. These operations are:  
- `write_frame_op`  
- `read_frame_op`  
- `ping_op`  
- `close_op`  
  
An operation is parked when it needs to read or write, and doing so would conflict with another operation. Consider the case where `read_frame_op` encounters a ping frame. `websocket::stream` automatically handles this case for you, by sending a pong in response. But what if you already have a write pending (such as an active call to `stream::async_write`? In this case, the implementation "parks" the read operation in the invokable. Later, when that pending write completes, it resumes the read operation by executing the invokable. That happens near the end of each operation. For example, near the end of `write_frame_op`:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/write_frame_op.ipp#L266  
  
There is one invokable slot for read, and one slot for write. Your program is crashing because the implementation tried to park an operation into an invokable which is already occupied, which breaks an invariant. `websocket::stream` allows only one pending asynchronous read and one pending asynchronous write.  
  
These functions count as reads:  
- `websocket::stream::async_read`  
- `websocket::stream::async_read_frame`  
  
These functions count as writes:  
- `websocket::stream::async_write`  
- `websocket::stream::async_write_frame`  
- `websocket::stream::async_ping`  
- `websocket::stream::async_close`  
  
One obvious way that comes to mind for this to happen, is if you are trying to execute two write operations at the same time. `async_close` counts as a write. Are you calling `async_write` and `async_close` before either of them complete? That would cause the assertion.  
  
Please study your code and make sure that you are not issuing multiple calls to functions that read or write, without waiting for one of them to complete. Also, please verify that all calls to `websocket::stream` functions are protected by an implicit or explicit strand. The easiest way to do that is to allow only one thread to call `io_service::run`. And, please verify that you are not calling asynchronous `websocket::stream` functions concurrently. Doing any of these previously mentioned things will break invariants and can cause the assertion you are experiencing.  
  
If you are certain that you are not breaking any of the stream invariants, the next step unfortunately is that you will have to help diagnose the problem. The best thing you can provide is a small test program that duplicates the defect. Other than that, any additional information you can find out from debugging would be helpful. I can also take a look at your source code if you create a private repository and add me as a collaborator (add my GitHub id _vinniefalco_). Any access you provide will be considered confidential.  
  
It would be nice to know which call to `invokable::emplace` is generating the assertion failure. These are all the places where it is called:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/close_op.ipp#L140  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/ping_op.ipp#L139  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read_frame_op.ipp#L330  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read_frame_op.ipp#L370  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read_frame_op.ipp#L486  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/write_frame_op.ipp#L181  
  
If you can instrument the program to add some additional logging or parameters to emplace so that we know which line caused the failure, it can help us diagnose the problem.

---

## Comment 5

> Username: goflatworld  
> Created at: 2016-10-20 07:25:52 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255029356  

I'll review the code and continue test it. That might take some days. Thanks for the detailed explanation of how it works.

---

## Comment 6

> Username: goflatworld  
> Created at: 2016-10-20 22:50:16 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255249588  

I got the feeling that this might be caused by bad network connection although I can not confirm this yet. The reason I get the feeling is that when my network is good, the application doesn't crash. When my network is bad (when I say bad, I tried to open any web page, it times out). The server crash quite often.   
  
I'll keep test it for few more days to see whether this feeling is correct or not.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-10-20 22:51:48 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255249834  

Still, bad network conditions should not cause an application failure. Probably you are not handling errors correctly (the `error_code&` params on completion handlers). Shutting down sockets cleanly is always difficult in async programs.

---

## Comment 8

> Username: goflatworld  
> Created at: 2016-10-20 22:53:22 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255250091  

Thanks for the tips, I'll check the socket shutdown.

---

## Comment 9

> Username: goflatworld  
> Created at: 2016-10-24 22:03:55 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255879659  

I have uploaded my project to github in private mode and have added you to view the code.  
  
https://github.com/goflatworld/Stingray  
  
Can you please review the code and see what I'm doing wrong in there?  
  
The server still crash quite often sometimes.  
  
Thanks  
  
George

---

## Comment 10

> Username: goflatworld  
> Created at: 2016-10-25 09:17:36 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255982057  

Can you please let me know whether you can access the code or not?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2016-10-25 09:47:57 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255989022  

Yes, I can see the code. But its a big program and nothing immediately jumps out at me.

---

## Comment 12

> Username: goflatworld  
> Created at: 2016-10-25 09:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-255989940  

Thanks for looking into it.  
  
The key code is in the session.cpp. In this file, I have two parts, one handles normal socket. One handles HTTP socket. The crash is in the HTTP socket part.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2016-10-25 12:15:33 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256018101  

You're missing a strand here:  
https://github.com/goflatworld/Stingray/blob/master/session.cpp#L336

---

## Comment 14

> Username: goflatworld  
> Created at: 2016-10-26 07:41:28 UTC  
> Updated at: 2016-10-26 08:19:13 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256272758  

Thanks for pointing this out. I have modified the code and tested. It crashes with same error messages. During my test, I was fairly sure there were only two sockets connected to the server, one was the normal socket client and another one was the websocket client ( I verified this by keep running "netstat -an | grep 443" and I only saw two established tcp connections). I shouldn't have any async_close operation during the crash.  
  
My application is a telnet like application, the normal socket client sends only 6 chars  to the server in one async_write. The server decodes the packet and sends ONE char to the websocket in one async_write. So my write operation happens quite a lot (e.g. several hundreds -- even thousands-- a second). Could this contribute to the issue?   
  
The modified code is on github.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2016-10-26 08:40:01 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256284726  

There should be no problem sending many messages

---

## Comment 16

> Username: goflatworld  
> Created at: 2016-10-26 08:42:40 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256285297  

Thanks. Do you mind looking into the modified code to see if you can find out any issue?

---

## Comment 17

> Username: vinniefalco  
> Created at: 2016-10-26 08:50:21 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256287031  

I looked at it again, but nothing jumps out. It looks like if flag==0 then its a normal websocket connection but if flag==1 then its something else? Do you ever try to mix the two on the same connection?

---

## Comment 18

> Username: goflatworld  
> Created at: 2016-10-26 09:07:27 UTC  
> Updated at: 2016-10-26 09:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256290859  

If flag==0 it is a websocket, if flag==1, it is a normal socket.   
  
In my testing, I only had two connections, one was websocket, one was normal socket. I type command on the websocket client, sends to server, server sends to normal socket client. Normal socket client return result to server, server send to websocket. The crash happens when the packets are from server to websocket client.  
  
I have modified the code to give flag a -1 value just in case flag might have a default value of 0 or 1. It still crashes though.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2016-10-26 09:17:18 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256293124  

You're going to have to debug it. What's the crash? Is there a stack?

---

## Comment 20

> Username: goflatworld  
> Created at: 2016-10-26 09:19:35 UTC  
> Updated at: 2016-10-26 09:22:56 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256293621  

The process stops with below errors printed on the screen, that's all I have at this stage. I have to re-run the application server. --- without websocket (e.g. if I use two normal socket, there was no crash).  
  
server: /usr/include/beast/websocket/detail/invokable.hpp:138: void beast::websocket::detail::invokable::emplace(F&&) [with F = beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_frame_op<beast::prepared_buffers<beast::consuming_buffers<boost::asio::const_buffers_1, boost::asio::const_buffer> >, beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_op<boost::asio::const_buffers_1, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, boost::_bi::bind_t<void, boost::_mfi::mf1<void, websocket::applications::chat::session, const boost::system::error_code&>, boost::_bi::list2boost::_bi::value<std::shared_ptr<websocket::applications::chat::session >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running> > >]: Assertion `! base_' failed.

---

## Comment 21

> Username: vinniefalco  
> Created at: 2016-10-26 09:31:23 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256296351  

Oh, right - that. I already gave you some instructions, did you have the chance to follow them? I will repeat it here:  
  
> If you are certain that you are not breaking any of the stream invariants, the next step unfortunately is that you will have to help diagnose the problem. The best thing you can provide is a small test program that duplicates the defect. Other than that, any additional information you can find out from debugging would be helpful. I can also take a look at your source code if you create a private repository and add me as a collaborator (add my GitHub id vinniefalco). Any access you provide will be considered confidential.  
>   
> It would be nice to know which call to invokable::emplace is generating the assertion failure. These are all the places where it is called:  
> https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/close_op.ipp#L140  
> https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/ping_op.ipp#L139  
> https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read_frame_op.ipp#L330  
> https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read_frame_op.ipp#L370  
> https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read_frame_op.ipp#L486  
> https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/write_frame_op.ipp#L181  
>   
> If you can instrument the program to add some additional logging or parameters to emplace so that we know which line caused the failure, it can help us diagnose the problem.

---

## Comment 22

> Username: goflatworld  
> Created at: 2016-10-26 09:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256297770  

Thanks. I'll try to think about how to debug it.

---

## Comment 23

> Username: vinniefalco  
> Created at: 2016-10-26 09:43:58 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256299197  

You can edit invokable.hpp and make copies of the `emplace` function, name them `emplace1`, `emplace2`, etc..  and then from each place where `emplace` is called, call one of the new functions (uniquely). Then edit the BOOST_ASSERT in the body of each `emplace` function to put some descriptive text for example  
  
```  
BOOST_ASSERT((! base_) && "1");  
```  
  
or  
  
```  
BOOST_ASSERT((! base_) && "2");  
```  
  
etc...

---

## Comment 24

> Username: goflatworld  
> Created at: 2016-10-26 09:55:55 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256301943  

Thanks for the hint, here is my understanding on how to do it:  
1. Modify the .hpp file as below:  
  
.........................................................................  
  
template<class F>  
void  
invokable::emplace_1(F&& f)  
{  
    static_assert(sizeof(buf_type) >= sizeof(holder<F>),  
        "buffer too small");  
    BOOST_ASSERT(! base_, && "1");  
    ::new(buf_) holder<F>(std::forward<F>(f));  
    base_ = reinterpret_cast<base*>(&buf_[0]);  
}  
  
template<class F>  
void  
invokable::emplace_2(F&& f)  
{  
    static_assert(sizeof(buf_type) >= sizeof(holder<F>),  
        "buffer too small");  
    BOOST_ASSERT(! base_, && "2");  
    ::new(buf_) holder<F>(std::forward<F>(f));  
    base_ = reinterpret_cast<base*>(&buf_[0]);  
}  
  
.........................................................................  
1. In https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/close_op.ipp#L140  
  
I modify emplace to emplace_1  
  
In https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/ping_op.ipp#L139  
I modify emplace to emplace_2  
1. Repeat step 1 and 2, until i modify "emplace" to emplace_6.  
  
If this is correct, I'll do it now and test it out.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2016-10-26 10:08:17 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256304653  

Yes! Don't forget about the ones in `read_frame_op.ipp`

---

## Comment 26

> Username: goflatworld  
> Created at: 2016-10-26 10:16:37 UTC  
> Updated at: 2016-10-26 10:20:11 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256306429  

Done and tested twice, it is this line called emplace and crashed --- I have modifed write_frame_opp.ipp to emplace_6.     
  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/write_frame_op.ipp#L181  
  
server: /usr/include/beast/websocket/detail/invokable.hpp:215: void beast::websocket::detail::invokable::emplace_6(F&&) [with F = beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_frame_op<beast::prepared_buffers<beast::consuming_buffers<boost::asio::const_buffers_1, boost::asio::const_buffer> >, beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_op<boost::asio::const_buffers_1, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, boost::_bi::bind_t<void, boost::_mfi::mf1<void, websocket::applications::chat::session, const boost::system::error_code&>, boost::_bi::list2boost::_bi::value<std::shared_ptr<websocket::applications::chat::session >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running> > >]: Assertion `! base_ && "6"' failed.

---

## Comment 27

> Username: vinniefalco  
> Created at: 2016-10-26 10:24:41 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256308131  

So, that will happen if you try to call `async_write` a second time without getting a completion for the first call.

---

## Comment 28

> Username: goflatworld  
> Created at: 2016-10-26 10:32:56 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256309731  

All my async_write is in the session.cpp and they have strand_.wrap. What could it be then?  
  
flatworld@FlatWorld:~/workspace/Stingray/V2/beast$ grep async_write *  
grep: lib: Is a directory  
Binary file server matches  
Binary file serverbak matches  
session.cpp:                wssocket_.async_write(  
session.cpp:                boost::asio::async_write(wssocket_.next_layer(),  
session.cpp:                boost::asio::async_write(wssocket_.next_layer(),  
grep: xterm.js: Is a directory

---

## Comment 29

> Username: vinniefalco  
> Created at: 2016-10-26 10:40:04 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256311123  

I don't know! You're going to have to debug it. Try checking a bool before calling `websocket_.async_write`, set the bool before the call, and clear it in the completion, to make sure you aren't calling it twice in a row.  
  
Try adding some logging, for every time you call async_write. Try getting a breakpoint at the moment of the assert and see what other threads are doing. I can't really help much, you need to debug it and get more information.

---

## Comment 30

> Username: goflatworld  
> Created at: 2016-10-26 10:50:24 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256313182  

Just let you know that I commented out BOOST_ASSERT just for testing purpose.   
  
//    BOOST_ASSERT(! base_ && "6");  
  
Now it appears OK... I used to run a command which generates around 640KB data (meaning 640,000 write operations). Before the change, it crashes several times to get it done once. Now, I have finished two commands without any issue.  
  
I'm not sure what happened. I understand it might be too early to say it works and understand it is probably not the way the software should be written.   
  
I'll test for a few days to see if I have any crashes or not and update here.

---

## Comment 31

> Username: vinniefalco  
> Created at: 2016-10-26 10:58:16 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256314761  

I believe I see the problem:  
https://github.com/goflatworld/Stingray/blob/master/session.cpp#L515  
  
This code (channel.cpp) can call `session::deliver` twice in a row, without waiting for the completion. As I said earlier:  
  
> Please study your code and make sure that you are not issuing multiple calls to functions that read or write, without waiting for one of them to complete.   
  
If you want to call `deliver` without waiting, then you need a queue of `chat_message` and make sure you only send one at a time.

---

## Comment 32

> Username: goflatworld  
> Created at: 2016-10-26 11:11:11 UTC  
> Updated at: 2016-10-26 11:12:26 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256317129  

Great thanks for this one. I'll think about it on how to modify it.  Will take your idea of a queue like what has been done in the normal socket for the deliver function. Thanks for your great help and patience. I also think this should be the root cause.  
  
Meanwhile, just let you know, the application is still running OK at the moment, I have finished three commands without any issue. Something for me to learn in the future as well.   
  
Again, thanks for our great help and patience. I really appreciated your help.

---

## Comment 33

> Username: vinniefalco  
> Created at: 2016-10-26 11:13:45 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256317631  

Your app will seem to function with the assert turned off, but the moment the client starts sending pings and pongs you will probably crash.

---

## Comment 34

> Username: goflatworld  
> Created at: 2016-10-26 11:19:23 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256318702  

Thanks for the explanation. It makes total sense now.

---

## Comment 35

> Username: goflatworld  
> Created at: 2016-10-27 01:45:32 UTC  
> Url: https://github.com/boostorg/beast/issues/147#issuecomment-256524951  

So far, it works like charm! You have written a great library.
