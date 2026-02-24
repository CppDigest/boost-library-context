# #2145 - beast::tcp_stream timeout results in CLOSE_WAIT state [Closed]

> Username: jdwlynch-zz  
> Created at: 2021-01-06 17:30:48 UTC  
> Updated at: 2021-01-06 19:05:43 UTC  
> Closed at: 2021-01-06 19:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2145  

Beast version: 300  
Boost version: 1.74.0  
  
When compiling a file server using Beast and Asio with g++10, I have an odd issue.  
  
A developer is using Axios to build a rudimentary front-end to test some functionality. I noticed that the sockets aren't being closed, and are rather timing out.  
  
I'm using:   
  
```stream_.expires_after(std::chrono::seconds(1));```  
  
a fairly fast timeout until it's deemed we need otherwise.  I initially noticed this with a 30 second timeout.  
  
The problem is, when I try to access this page through localhost on Chrome or Chromium based browsers, the sockets hang in the CLOSE_WAIT state.  I confirmed this with netstat.  
  
If I use Firefox, the "timed-out" sockets transition to the TIME_WAIT state.  
  
TIME_WAIT isn't a problem since I'm using allow_reuse, however CLOSE_WAIT is a problem.  From what I'm seeing, it would be pretty easy to DOS this server by just opening a bunch of client side sockets until I hit the 1024 FD limit.  
  
Is this expected behaviour? Is there an option I should be using or something to avoid this? Is timeout the right tool for this? This problem doesn't happen when I curl the endpoints. When using Curl, my server detects shutdown_send, and cleans the socket up appropriately.  
  
It's the client-side part that hangs open, clogging things up with the CLOSED_WAIT state.    
  
FYI, I've gone through the Chat Server example and picked things out to get to this point.  
  
Thanks for the help. I appreciate it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-01-06 17:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2145#issuecomment-755445748  

Yes that's normal for Chrome. The browser does not close connections gracefully.

---

## Comment 2

> Username: jdwlynch-zz  
> Created at: 2021-01-06 17:38:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2145#issuecomment-755449477  

Thanks very much for the quick reply!  
  
Do you have any suggestions on how to avoid CLOSE_WAIT sockets piling up if a client decides to misbehave and never send FIN?  I've been deep in the weeds with documentation including different socket options but nothing is jumping out at me.  
  
Is this just a weakness of a TCP server due to the restrictions of the RFC?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-01-06 17:43:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2145#issuecomment-755452535  

You can remove the CLOSE_WAIT state by manually closing the socket on the server end.  
This state indicates that the OS knows that there will be no more communication on this socket but your program hasn't released the last handle to it.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-01-06 17:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2145#issuecomment-755453543  

CLOSE_WAIT doesn't bother servers the same way it bothers clients, so I would not worry about it.

---

## Comment 5

> Username: jdwlynch-zz  
> Created at: 2021-01-06 19:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2145#issuecomment-755530098  

> You can remove the CLOSE_WAIT state by manually closing the socket on the server end.  
> This state indicates that the OS knows that there will be no more communication on this socket but your program hasn't released the last handle to it.  
  
I could try that with a manual timeout and forcing a close.  
  
> CLOSE_WAIT doesn't bother servers the same way it bothers clients, so I would not worry about it.  
  
I suspect this is correct. I created a mini torture test where I opened and hung 20k connections simultaneously. The dangling CLOSE_WAIT didn't appear to be a problem. I'll need to do some reading to understand why the server can deal with this, but thank you both for pointing me in the right direction.
