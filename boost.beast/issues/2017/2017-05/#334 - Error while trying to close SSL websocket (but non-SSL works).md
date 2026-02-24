# #334 - Error while trying to close SSL websocket (but non-SSL works) [Closed]

> Username: vchang-akamai  
> Created at: 2017-05-02 22:40:10 UTC  
> Updated at: 2017-05-10 02:21:46 UTC  
> Closed at: 2017-05-10 02:21:46 UTC  
> Url: https://github.com/boostorg/beast/issues/334  

My server uses a Beast SSL websocket with all async_* APIs wrapped with an explicit strand (though for right now I'm only running with one thread).  My test client just uses the synchronous APIs.  
  
On the server, after SSL handshaking and websocket upgrade are completed, it is always async_read()'ing for new incoming messages from the client (the callback will call async_read() again to set up the same read callback).   
  
On the test client, I call beast::websocket::stream::close(), followed by  beast::websocket::stream::read() until I get beast::websocket::error::closed.  However, on the server side, the read callback handler gets an ASIO EOF error code which apparently means there was a short read. And on the client side, my read() also keeps getting the ASIO EOF error code.  
  
When I don't use SSL (just beast::websocket::stream<boost::asio::ip::tcp::socket>), the closing works fine.  
  
Why does this work for non-SSL, but not for SSL?  
  
I noticed this other issue: #309 .  Might be related, but not entirely sure.  
  
I'm using Beast 1.0.0-b34

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-02 22:48:30 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298782800  

After your client gets `beast::websocket::error::closed` you are always going to get `boost::asio::error::eof` on each subsequent read, until you create a new connection with the socket. That's because when Beast delivers the "closed" error, it has already gone through the "teardown" process which closes the underlying TCP/IP socket gracefully. This is prescribed by rfc6455 (and also a shortcoming of it but I didn't write the rfc).  
  
On the server side, when a pending asynchronous read receives a close frame, execution transfers to here:  
https://github.com/vinniefalco/Beast/blob/6d5547a32c50ec95832c4779311502555ab0ee1f/include/beast/websocket/impl/read.ipp#L466  
  
I'm not sure what's happening but if you want to try your luck and stepping through it or otherwise logging, it could provide more information. The asynchronous operation implementation for the teardown call is here:  
https://github.com/vinniefalco/Beast/blob/6d5547a32c50ec95832c4779311502555ab0ee1f/include/beast/websocket/impl/ssl.ipp#L103  
  
The top of that file provides a link which explains how the SSL teardown is supposed to work. Maybe I screwed it up? Its possible. Getting shutdown right is always a hassle.  
  
On the other hand, are you sure that you the client is not sending any additional data after the close frame? If so, that could cause this problem.

---

## Comment 2

> Username: vchang-akamai  
> Created at: 2017-05-03 00:10:50 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298795522  

My test client never got the beast::websocket::error::closed error code in the SSL case. It just kept getting the ASIO EOF code.  
  
I'm not aware of the test client sending anything after the close frame.  It calls the synchronous close(), then does the synchonous read().   Not sure if there's anything else happening underneath in Beast, SSL or ASIO...  
  
I was able to set some gdb breakpoints in my server:  
  
1. read.ipp:466  
1. ssl.ipp:108  
1. the start of the server's websocket read handler  
  
Ran the server in gdb. Ran my test client. Observed:  
1. Break at read.ipp:466. I hit "c" to continue  
1. Break at ssl.ipp:108.  Continue again  
1. At some point here, the test client's read() gets the ASIO EOF code  
1. Break again at ssl.ipp:108.  Continue yet again  
1. Break at my server's websocket read handler. The error code provided by Beast is the ASIO EOF  
  
I'm not familiar with Beast internals, so didn't step through much in detail in read.ipp or ssl.ipp.  Anything you recommend to check in there?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-03 00:22:21 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298796935  

If the server reaches ssl.ipp:108 then your server's websocket read handler should not attempt to perform any more asynchronous operations. You need to close the socket and destroy it. Your read handler should be seeing `beast::websocket::error::closed`. That error is generated here:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/read.ipp#L602  
  
If your server got to ssl.ipp:108 then it should subsequently reach read.ipp:604, and your read completion handler should see `error::closed`.

---

## Comment 4

> Username: vchang-akamai  
> Created at: 2017-05-03 00:58:10 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298801198  

I added a breakpoint at read.ipp:604 (in addition to the 3 breakpoints I had earlier) and it was not reached.   
  
The server's websocket read handler currently destroys the socket upon error. (The actual beast websocket and the read handler are members/methods of a class that is managed by a shared_ptr.  After the read handler sees the error code, the shared_ptr reference count goes to zero and the class' destructor is called)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-05-03 01:12:53 UTC  
> Updated at: 2017-05-03 01:14:17 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298802800  

If read.ipp:604 is not reached, it means that `call_async_teardown` is generating an error code. You can log it like this. At the top of that function (line 171) add this code:  
```  
if((d.state == do_teardown + 1) && ec)  
  std::cerr << "do_teardown + 1: " << ec.message() << std::endl;  
```  
  
However, in the original issue you said you're getting `boost::asio::error::eof`? That shouldn't be happening. `eof` is different from a short read. I suggest reading this Stack Overflow post and see if your code is meeting the requirements:  
http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
  
Then again maybe Beast is doing the wrong thing? Check this out:  
http://stackoverflow.com/questions/32046034/what-is-the-proper-way-to-securely-disconnect-an-asio-ssl-socket/32054476#32054476

---

## Comment 6

> Username: vchang-akamai  
> Created at: 2017-05-03 04:44:13 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298822266  

I added your suggested debug message and got: "do_teardown + 1: End of file". This occurred right before my server's websocket read handler was called with the boost::asio::error::eof error code.  
  
I've read both of the stackoverflow links you suggested.  I've also read your other comment in another issue: [https://github.com/vinniefalco/Beast/issues/309#issuecomment-296787459](https://github.com/vinniefalco/Beast/issues/309#issuecomment-296787459) regarding the sequence of API calls for a proper closure of the Websocket, SSL, TCP connection.  
  
However, I'm getting the ASIO EOF with both client and server read()/async_read() before I even have a chance to attempt the SSL shutdown/async_shutdown().

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-05-03 13:41:32 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298913940  

I am investigating, thank you for looking into it.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-05-03 16:44:17 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298967862  

I believe this branch reproduces the issue:  
https://github.com/vinniefalco/Beast/commits/ssl

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-05-03 17:02:37 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-298972790  

@vchang-akamai It seems to me that `boost::asio::error::eof` is normal when a clean SSL shutdown handshake takes place. This is based on a reading of http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error, specifically **PartyA initiates shutdown() and waits for PartyB to respond with a shutdown()**. Therefore, Beast's behavior is incorrect. Working on a fix...

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-05-03 18:58:36 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299003808  

@vchang-akamai I believe this branch will fix your issue, thanks for helping me get rid of a defect!!!  
https://github.com/vinniefalco/Beast/commits/ssl

---

## Comment 11

> Username: vchang-akamai  
> Created at: 2017-05-04 00:04:44 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299068785  

I'm trying out your fix... It appears to be based on a newer branch with unrelated API changes.  I've updated my test client's handshake() to use handshake_ex() with the request decorator.  However, I'm stuck on some compile problems on my server relating to beast::websocket::async_accept().  
  
My code looks something like:  
  
`websock_.async_accept(httpReq_, strand_.wrap(boost::bind(&WebsocketSession::handleWebSockUpgrade, shared_from_this(),  boost::asio::placeholders::error)))`  
  
where "httpReq_" is a  `beast::http::request<beast::http::string_body>`  
  
The compile error I'm getting (GCC 4.8.4):  
```  
[....]/include/beast/websocket/impl/accept.ipp:640:5: error: static assertion failed:  
ConstBufferSequence requirements not met  
     static_assert(is_ConstBufferSequence<  
```  
  
It seems the compiler is matching to this overload of async_accept:  
  
```  
template<class NextLayer>  
template<class ConstBufferSequence, class AcceptHandler>  
typename async_completion<AcceptHandler,  
    void(error_code)>::result_type  
stream<NextLayer>::  
async_accept(ConstBufferSequence const& buffers,  
    AcceptHandler&& handler)  
```  
instead of:  
  
```  
template<class NextLayer>  
template<class Fields, class AcceptHandler>  
typename async_completion<AcceptHandler,  
    void(error_code)>::result_type  
stream<NextLayer>::  
async_accept(http::header<true, Fields> const& req,  
    AcceptHandler&& handler)  
```

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-05-04 00:11:44 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299069745  

Yikes, I think you found a quirk with this interface. Try passing `httpReq_.base()` instead!

---

## Comment 13

> Username: vchang-akamai  
> Created at: 2017-05-04 00:23:02 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299071188  

Ok, I finally got it to compile and I tested it out. Appears to be fixed!  
  
Thanks for your help on this!  Your responsiveness is amazing; it's like you're sitting nearby :-)

---

## Comment 14

> Username: vchang-akamai  
> Created at: 2017-05-04 00:26:27 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299071617  

BTW, do you know when this fix (and the other recent changes) will be merged into master?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-05-04 00:44:55 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299073883  

As soon as the code gets reviewed. There are a lack of volunteers, perhaps you could do a code review of the **b35** branch that is sitting in the pull request queue? That would be VERY helpful! The next few branches are already ready (b35, b36, b37, and this one will be b38) I am just waiting on reviews. There are API changes. Feedback from stakeholder (thats you!) is crucial to make sure the quality stays high. Any participation is appreciated!

---

## Comment 16

> Username: vchang-akamai  
> Created at: 2017-05-04 23:09:24 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299333750  

I've started skimming through the b35 branch in the pull request queue.  How rigorous is this review process? I'm a Beast newbie, so this feels more of a learning exercise for me, rather than a serious bug hunt...

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-05-04 23:26:00 UTC  
> Url: https://github.com/boostorg/beast/issues/334#issuecomment-299336177  

Its whatever you want it to be. Even if all you do is ask questions which I can answer, to help you understand the implementation, thats a win. If you see a bug or obvious improvement, thats an even bigger win. And if you provide feedback on public interfaces, like for example what you want them to do or if you think functionality is missing or could be exposed in leaner or more obvious ways thats a home run!
