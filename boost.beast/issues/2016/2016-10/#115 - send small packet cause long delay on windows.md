# #115 - send small packet cause long delay on windows [Closed]

> Username: cppevrywhr  
> Created at: 2016-10-08 15:10:18 UTC  
> Updated at: 2016-11-01 17:33:58 UTC  
> Closed at: 2016-11-01 17:33:58 UTC  
> Url: https://github.com/boostorg/beast/issues/115  

in autobahn test case 1.1.1, autobahn client received all data at once from async/sync echo server when the socket was closed.  
I've tried turning on TCP_NODELAY, but the result remains same.   
There is also a register key(TcpAckFrequency) specifying the number of ACKs that will be outstanding before the delayed ACK timer is ignored, but it requires a restart of system.  
  
tested on win7/win10 64bit, msvc14.0, boost 1.62.0, autobahn 0.7.5

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-08 15:57:20 UTC  
> Updated at: 2016-10-08 15:59:06 UTC  
> Url: https://github.com/boostorg/beast/issues/115#issuecomment-252432412  

You mean this test case?  
http://vinniefalco.github.io/autobahn/sync_echo_server_case_1_1_1.html  
  
I don't understand the problem - you said that the client "received all data at once." This is correct behavior, the test sends a single message with payload size zero. The test also indicates "wasClean==True", meaning that the close frames were properly sent and received.  
  
What exactly is the problem here?

---

## Comment 2

> Username: cppevrywhr  
> Created at: 2016-10-08 16:18:05 UTC  
> Url: https://github.com/boostorg/beast/issues/115#issuecomment-252433555  

case 1.1.1  requires first message to be echoed within 1 sec. The wire log should looks like TX,RX,TX,RX. While in my case, it is TX,TX,RX,RX.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-08 16:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/115#issuecomment-252433708  

Okay, so this problem only happens on your configuration? Are you running the echo server from the debugger? Are you running a Release build? Do you have `_NO_DEBUG_HEAP=1` set in your Environment (project properties)?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-10-08 17:13:09 UTC  
> Url: https://github.com/boostorg/beast/issues/115#issuecomment-252436601  

I just re-ran the Autobahn test suite, on Windows 10 using the **RelWithDbgInfo** target, and `_NO_DEBUG_HEAP=1` in the project environment. There were no failures:  
http://vinniefalco.github.io/autobahn/index.html

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-10-14 21:50:50 UTC  
> Url: https://github.com/boostorg/beast/issues/115#issuecomment-253928335  

Were you able to gather any additional information on this issue?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-11-01 17:33:58 UTC  
> Url: https://github.com/boostorg/beast/issues/115#issuecomment-257634033  

I've run the Autobahn test suite many times and I have not been able to reproduce your issue. Without additional information, there is nothing I can do. If you have more information, feel free to re-open this or create a new issue, thanks.
