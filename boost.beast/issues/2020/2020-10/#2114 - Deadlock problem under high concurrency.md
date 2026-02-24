# #2114 - Deadlock problem under high concurrency [Closed]

> Username: Yyyyshen  
> Created at: 2020-10-27 13:55:15 UTC  
> Updated at: 2022-01-09 05:17:09 UTC  
> Closed at: 2022-01-09 05:17:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2114  

We write a http example with `beast`, and we use multiple threads to run `io_service`.  
Then we conduct a large number of concurrent connection tests. Each connection will only send and receive packets once, and then disconnect.  
Later, we found that it is difficult to connect to the opened ports.  
We think that a deadlock occurred in `async_accept`.Because if we use single thread, this problem will not happen.  
Is there any solution for this problem？  
I would very appreciate it if who can help me.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-27 15:27:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717322527  

Do the asynchronous example servers deadlock for you?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-27 15:27:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717323165  

Also, have a look at this: http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html

---

## Comment 3

> Username: Yyyyshen  
> Created at: 2020-10-28 03:08:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717666725  

> Do the asynchronous example servers deadlock for you?  
  
We are using asynchronous example.  
  
  
> Also, have a look at this: http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html  
  
We have the following configuration ,and we have checked that there is no TIME_WAIT.  
```  
		boost::system::error_code ec;  
		static boost::asio::ip::tcp::no_delay no_delay(true);  
		socket.set_option(no_delay, ec);  
		socket.set_option(boost::asio::socket_base::linger(true, 0), ec);  
		socket.set_option(boost::asio::socket_base::reuse_address(true), ec);  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-10-28 04:02:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717681483  

What Operating System?

---

## Comment 5

> Username: Yyyyshen  
> Created at: 2020-10-28 05:36:45 UTC  
> Updated at: 2020-10-28 05:50:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717710405  

> What Operating System?  
  
`Windows`  `boost1.74`  
Especially in the case of using SSL, it is more likely to occur.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-10-28 09:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717808313  

> We think that a deadlock occurred in async_accept  
  
Q1: are you protecting the async_accept with a strand?  
Q2: are you checking for connection_aborted error and responding by listening again? This is a special case error that is more of a notification.

---

## Comment 7

> Username: Yyyyshen  
> Created at: 2020-10-28 09:54:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717822754  

> > We think that a deadlock occurred in async_accept  
>   
> Q1: are you protecting the async_accept with a strand?  
> Q2: are you checking for connection_aborted error and responding by listening again? This is a special case error that is more of a notification.  
  
A1: Yes. like this:  
`: acceptor_(net::make_strand(ioc)) //initialization list`  
```  
acceptor_.async_accept(  
		net::make_strand(ioc_),  
		beast::bind_front_handler(  
			&ClassName::on_accept,  
			shared_from_this())); //call async_accept  
```  
A2:Of course.  But in fact, when this problem happen, there is no error occur.  
  
In addition, we use your official example for testing, this problem will also occur.  
  
The hardware configuration of our test machine is 48-core 64G memory, and we use 70,000 ips for endless loop http requests, this problem will appear. No error occurs, but the port cannot be connected.  
We are firewall developers and we are troubled by this.  
If there is any good suggestions, it would be very appreciated.

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-10-28 11:56:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-717884958  

Please create a small GitHub repo containing the server and the test client/runner.  
  
I am happy to take a look at this running on a local machine of similar spec.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-10-28 15:48:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718025338  

If you are using Windows Professional or Windows Home, the network stack is intentionally limited from accepting a high rate of incoming connections. In order to achieve maximum connectivity, you must have a license for Windows 10 Server.

---

## Comment 10

> Username: Yyyyshen  
> Created at: 2020-10-28 16:05:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718036696  

> Please create a small GitHub repo containing the server and the test client/runner.  
>   
> I am happy to take a look at this running on a local machine of similar spec.  
  
This is our demo repo,   
The description is placed in the Readme file  
https://github.com/Yyyyshen/HttpsDemoWithBeast  
  
  
> If you are using Windows Professional or Windows Home, the network stack is intentionally limited from accepting a high rate of incoming connections. In order to achieve maximum connectivity, you must have a license for Windows 10 Server.  
  
We do use win10 server for testing.  
  
Thanks again for your continued attention.

---

## Comment 11

> Username: MechanicsMingYan  
> Created at: 2020-10-28 16:14:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718042903  

I have encountered this problem, but I did not find the specific cause, but the phenomenon is consistent with the current discussion and the scene

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-10-28 16:37:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718058004  

You might need to use multiple listening sockets, one per thread. This is more of an asio issue than beast, since beast does not provide listening sockets (although the examples use them).

---

## Comment 13

> Username: Yyyyshen  
> Created at: 2020-10-29 01:58:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718310211  

> You might need to use multiple listening sockets, one per thread. This is more of an asio issue than beast, since beast does not provide listening sockets (although the examples use them).  
  
We tried this way, the effect is not ideal.  
  
The test client code has pushed in the repo now. https://github.com/Yyyyshen/HttpsDemoWithBeast  
And there are some pictures showing our test situation.  
After the port cannot be connected, the accept will restart only after all sessions are released.

---

## Comment 14

> Username: Yinpinghua  
> Created at: 2020-10-29 14:19:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718784363  

I have the same problem

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-10-29 14:25:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718787883  

Do the asio server examples have the same problem? They are here:  
https://github.com/boostorg/asio/tree/develop/example

---

## Comment 16

> Username: Yyyyshen  
> Created at: 2020-10-29 14:42:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718799288  

> Do the asio server examples have the same problem? They are here:  
> https://github.com/boostorg/asio/tree/develop/example  
  
Yes, we have tried.   
But beast is still based on asio after all. Can you help us advance this issue?

---

## Comment 17

> Username: madmongo1  
> Created at: 2020-10-29 14:48:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718803265  

ok I have it compiling. What paramters should I use for the client in order to demonstrate the problem?

---

## Comment 18

> Username: Yyyyshen  
> Created at: 2020-10-29 14:56:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718808381  

> ok I have it compiling. What paramters should I use for the client in order to demonstrate the problem?  
  
example:  
.\AsioHttpTest.exe 127.0.0.1 443 15 10000 1 www.upk.net  
127.0.0.1 for local testing  
But generally multiple machines are required to run the client.   
Then change 127.0.0.1 to the ip of the server program.  
Under win10 server with 48 cores and 64g,  
It will happen when the number of online reaches about 8000.

---

## Comment 19

> Username: madmongo1  
> Created at: 2020-10-29 15:25:50 UTC  
> Updated at: 2020-10-29 15:26:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718828007  

I am seeing this in the client:  
```  
on-line:30002 connerror:0 sslerror:0 writeerror:0 readerror:131 ok:0  
on-line:30001 connerror:0 sslerror:0 writeerror:0 readerror:114 ok:0  
on-line:30001 connerror:0 sslerror:0 writeerror:0 readerror:130 ok:0  
on-line:30001 connerror:0 sslerror:0 writeerror:0 readerror:223 ok:0  
on-line:30001 connerror:0 sslerror:0 writeerror:0 readerror:788 ok:0  
on-line:30000 connerror:0 sslerror:0 writeerror:0 readerror:2259 ok:0  
on-line:30000 connerror:0 sslerror:0 writeerror:0 readerror:2146 ok:0  
on-line:29998 connerror:0 sslerror:0 writeerror:0 readerror:2213 ok:0  
on-line:29997 connerror:0 sslerror:0 writeerror:0 readerror:2217 ok:0  
on-line:29999 connerror:0 sslerror:0 writeerror:0 readerror:2207 ok:0  
on-line:29998 connerror:0 sslerror:0 writeerror:0 readerror:2132 ok:0  
on-line:29995 connerror:0 sslerror:0 writeerror:0 readerror:2113 ok:0  
on-line:29993 connerror:0 sslerror:0 writeerror:0 readerror:2120 ok:0  
```  
  
and this on the server  
```  
 online:21309 accept:651 sslQps:216 httpqps:182  
 online:21750 accept:697 sslQps:351 httpqps:244  
 online:21384 accept:950 sslQps:1503 httpqps:1472  
 online:20013 accept:848 sslQps:2210 httpqps:2230  
 online:18663 accept:794 sslQps:2186 httpqps:2158  
 online:17315 accept:811 sslQps:2147 httpqps:2118  
 online:16008 accept:971 sslQps:2155 httpqps:2274  
 online:15113 accept:1255 sslQps:2169 httpqps:2133  
 online:13802 accept:782 sslQps:2214 httpqps:2163  
 online:12386 accept:750 sslQps:2130 httpqps:2178  
 online:10685 accept:401 sslQps:2229 httpqps:2180  
 online:8974 accept:531 sslQps:2127 httpqps:2140  
 online:9746 accept:3004 sslQps:1935 httpqps:2060  
 online:12865 accept:4956 sslQps:1857 httpqps:1862  
```  
  
This is on a standard linux (Fedora 31) host.  
  
The errors seem to be system:2 (ENOENT) - "No such file or directory"

---

## Comment 20

> Username: Yyyyshen  
> Created at: 2020-10-29 15:34:33 UTC  
> Updated at: 2020-10-29 15:50:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718834040  

Under continuous high pressure, Is there a situation that the number of accept is zero?  
And only the number of online is cleared, accept begin to work again.  
Can you reproduce this situation.  
Maybe your test pressure is not enough.Do you need our cooperation.

---

## Comment 21

> Username: Yinpinghua  
> Created at: 2020-10-29 15:38:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718836655  

Under continuous high pressure, use the telnet command to specify the port, whether the port is

---

## Comment 22

> Username: Yyyyshen  
> Created at: 2020-10-29 15:53:40 UTC  
> Updated at: 2020-10-29 16:04:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718846564  

The situation in the picture occurred during our test.  
https://github.com/Yyyyshen/HttpsDemoWithBeast/blob/main/under_high_pressure%2Ccant_accept.png  
Our tests are all conducted on win10 server, will this be different.  
We use this file to optimize the registry , in order to eliminate system connection restrictions:  
https://github.com/Yyyyshen/HttpsDemoWithBeast/blob/main/Maximize%20the%20number%20of%20concurrent%20connections%20in%20the%20Windows.reg

---

## Comment 23

> Username: Yinpinghua  
> Created at: 2020-10-29 16:12:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718859184  

There is also a strange problem: sometimes the telnet port will work, sometimes it will not work

---

## Comment 24

> Username: Yyyyshen  
> Created at: 2020-10-29 16:18:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718863388  

The situation we tested is periodic,   
When the online number continues to be high, accept will stop receiving new connections.(accept count as 0)  
When the number of online is cleared(online count as 0), accept can continue to receive connections.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2020-10-29 16:19:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718863889  

What is your system backlog set to and what are you setting the backlog to in your socket?

---

## Comment 26

> Username: vinniefalco  
> Created at: 2020-10-29 16:19:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718864023  

See: https://stackoverflow.com/questions/36594400/what-is-backlog-in-tcp-connections

---

## Comment 27

> Username: madmongo1  
> Created at: 2020-10-29 16:30:39 UTC  
> Updated at: 2020-10-29 16:32:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718871068  

MSDN reference: https://docs.microsoft.com/en-us/windows/win32/api/winsock2/nf-winsock2-listen  
  
> The maximum length of the queue of pending connections. If set to SOMAXCONN, the underlying service provider responsible for socket s will set the backlog to a maximum reasonable value. **If set to SOMAXCONN_HINT(N) (where N is a number), the backlog value will be N, adjusted to be within the range (200, 65535). Note that SOMAXCONN_HINT can be used to set the backlog to a larger value than possible with SOMAXCONN.**  
  
I don't know if windows has been updated since this was written. On Linux I have been able to successfully accept 132,000 connections at once, but it required changing a kernel parameter (and used a lot of memory).  
  
reference: https://linux.die.net/man/2/listen  
  
Note that your program is specifying `SOMAXCONN` which for compatibiliy reasons is 128:  
  
```  
/* Maximum queue length specifiable by listen.  */  
#define SOMAXCONN	128  
```  
  
For the past 10 years or so, this is no longer the actual MAXCONN that can be acheived.

---

## Comment 28

> Username: Yyyyshen  
> Created at: 2020-10-29 16:39:41 UTC  
> Updated at: 2020-10-29 16:52:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718876527  

>On Linux I have been able to successfully accept 132,000 connections at once  
  
Is it using SSL? Is this the result of using our example?

---

## Comment 29

> Username: Yyyyshen  
> Created at: 2020-10-29 17:13:40 UTC  
> Updated at: 2020-10-29 18:21:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718897438  

We will make some adjustments and tests according to your guidance.  
  
But we have also tried other network frameworks before,   
under the same environment, the described problem will not occur

---

## Comment 30

> Username: madmongo1  
> Created at: 2020-10-29 18:33:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718942549  

> > On Linux I have been able to successfully accept 132,000 connections at once  
>   
> Is it using SSL? Is this the result of using our example?  
  
Yes it was SSL,   
No it was a server in a Bitcoin exchange. But built on similar principles.

---

## Comment 31

> Username: Yyyyshen  
> Created at: 2020-10-29 18:56:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-718955017  

> Yes it was SSL,  
> No it was a server in a Bitcoin exchange. But built on similar principles.  
  
Okay, we will do more attempts, including testing under linux.   
Thanks again.

---

## Comment 32

> Username: Yinpinghua  
> Created at: 2020-10-29 22:48:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-719067396  

good idea

---

## Comment 33

> Username: Yinpinghua  
> Created at: 2020-10-30 03:00:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-719141409  

@madmongo1 What configuration of your machine

---

## Comment 34

> Username: madmongo1  
> Created at: 2020-10-30 05:05:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-719179090  

6 cores, intel cpu, 64gb memory. Nothing special.

---

## Comment 35

> Username: Yinpinghua  
> Created at: 2020-10-30 05:25:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-719196524  

@madmongo1 You have set some system parameters under linux system

---

## Comment 36

> Username: madmongo1  
> Created at: 2020-10-31 06:45:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-719893841  

https://linux.die.net/man/2/listen

---

## Comment 37

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-751240915  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 38

> Username: Yyyyshen  
> Created at: 2021-01-15 09:06:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-760767897  

@madmongo1   
Hello, thank you for your previous help. Since there is a domain name certificate information in the following project, please delete the fork  
https://github.com/madmongo1/HttpsDemoWithBeast  
thank you

---

## Comment 39

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-850857902  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 40

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2114#issuecomment-1008232359  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
