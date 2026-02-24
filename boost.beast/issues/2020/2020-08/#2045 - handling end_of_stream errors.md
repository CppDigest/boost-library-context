# #2045 - handling end_of_stream errors [Closed]

> Username: ashjas  
> Created at: 2020-08-05 15:39:27 UTC  
> Updated at: 2020-09-20 04:28:09 UTC  
> Closed at: 2020-09-12 20:08:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2045  

Beast version 1.71  
  
Hello,  
  
we are using the async version of client for REST communication.  
The server keeps on closing the alive connections, at specific intervals, which isint in our control.  
This results in end_of_stream error at on_write().  
  
We have encapsulated the connect sessions say ```class beastsession;``` which includes:  
  
```  
    io_strand& m_strand;  
    tcp::resolver m_resolver;  
    beast::tcp_stream m_stream;  
    beast::flat_buffer m_buffer;  
```  
  
There is another class which manages these sessions, and their cleanups.  
say ```class Manager;```  
  
The issue we need to avoid is that in async flow, the end_of_stream should be handled gracefully within a single async initiate operation, so that , the user does not have to deal with retries in its code.  
  
We are checking the socket state in ```class Manager``` using ```stream.socket().is_open()```, and if it returns true, we assume that the connection would be valid, and we reuse this connect session, but which ultimately ends up in end_of_stream error.  
  
How can i check something else along with ```stream.socket().is_open()``` so that we can get to know if the write call would end up in ```end_of_stream``` error or in other words, that the server has already closed on this alive connection, so as to discard the session and create a new session for this request before attempting anything with the old session?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-05 16:34:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2045#issuecomment-669299258  

The problem with testing state before making a call is that the state can change during the call or during the write. Remember that the FIN packet sent by the server to close the TCP connection can arrive at any time.  
  
Therefore the only reliable way to handle the case where the tcp session has been closed is to attempt the send, and on the first error, re-connect and resend.  
  
You would then want to propagate subsequent errors back to the caller.  
  
In actual fact, the asio `is_open()` method merely tests to see whether the underlying socket handle is a valid socket FD (i.e. created with the sockets library call `socket()` .   
  
ref: https://www.man7.org/linux/man-pages/man2/socket.2.html

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-09-05 19:10:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2045#issuecomment-687650035  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-09-12 20:08:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2045#issuecomment-691539167  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-09-20 04:28:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2045#issuecomment-695699223  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
