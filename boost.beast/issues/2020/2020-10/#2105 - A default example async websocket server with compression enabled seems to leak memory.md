# #2105 - A default example async websocket server with compression enabled seems to leak memory [Closed]

> Username: 20k  
> Created at: 2020-10-16 07:26:42 UTC  
> Updated at: 2020-10-17 11:38:20 UTC  
> Closed at: 2020-10-17 11:38:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2105  

Version: 300 with boost 1.74  
  
Compiler: GCC 9.3.0 on ubuntu  
Running environment: 5.8.14-zen1-1-zen, arch  
  
I modified the sample code, adding  
  
```c++  
boost::beast::websocket::permessage_deflate opt;  
opt.server_enable = true;  
  
ws_.set_option(opt);  
```  
  
A full paste is available here: https://pastebin.com/ETWZ6kdN  
  
Then I spawn <30k simultaneous connections to the server using a driver program, which also has compression enabled, and uses binary mode. Each connection writes one message of ~200 bytes, and gracefully handles whatever response it gets. Connections are then forcefully terminated en masse (aka i just exit the program while its still running)  
  
The observed behaviour is that the memory usage of the server climbs without bound, well past 4GB. When the connections are cancelled, this memory is not recovered, although sometimes some portion of it is (eg 3GB might drop to 1.5GB). I'm using top and checking RES, just in case I'm a complete idiot! When compression is disabled, this doesn't happen  
  
I'm also not able to reproduce this issue on windows 10 in any form, which correctly seems to recover all the available memory. I'm also not unconvinced that this issue doesn't reproduce when compression is not enabled, but the memory allocation in that case would simply be minimal making it hard to notice  
  
Valgrind reports that all memory is still reachable  
  
==26494== LEAK SUMMARY:  
==26494==    definitely lost: 0 bytes in 0 blocks  
==26494==    indirectly lost: 0 bytes in 0 blocks  
==26494==      possibly lost: 441 bytes in 3 blocks  
==26494==    still reachable: 1,349,394 bytes in 6,609 blocks  
==26494==         suppressed: 0 bytes in 0 blocks  
==26494==  
==26494== ERROR SUMMARY: 3 errors from 3 contexts (suppressed: 0 from 0)  
  
Thanks!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-10-16 07:39:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709881910  

Is this with a debug build or release?

---

## Comment 2

> Username: 20k  
> Created at: 2020-10-16 07:53:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709888529  

The valgrind log was without optimisations, but this issue is present both in debug and release (-O2) builds. I should probably also note that the windows version which doesn't leak is compiled with GCC 10.2

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-10-16 08:10:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709896617  

Interesting. We have almost no platform-specific code in Beast. Most os the platform specific stuff is in Asio.  
@chriskohlhoff any immediate thoughts before I investigate?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-10-16 08:24:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709903570  

I am very skeptical.... there are no leaks in Beast.

---

## Comment 5

> Username: 20k  
> Created at: 2020-10-16 08:41:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709912276  

I can understand that, I spent all two days chasing down potential memory leaks in my application before I was able to consistently reproduce this with the sample code + compression being enabled. If you want any more information or investigating power I'm happy to help - I'd be pretty happy if it turned out that I simply messed something up - but I've ruled out everything I can think of on my end (triple checked the library version at runtime) except for some sort of system misconfiguration (or asio itself). The only unusual thing on the system is that its running the zen kernel, and it has a relatively old cpu (i5 750), but other than that I'm completely out of ideas on why the memory isn't being released

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-10-16 08:43:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709913125  

do you have the test client available? I'd like to see if I can reproduce.

---

## Comment 7

> Username: 20k  
> Created at: 2020-10-16 09:04:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709924026  

The test client is a giant mess, so I've knocked together a simple client that can reproduce this  
  
https://pastebin.com/grHmqHZX

---

## Comment 8

> Username: 20k  
> Created at: 2020-10-16 09:19:53 UTC  
> Updated at: 2020-10-16 09:26:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709931890  

I did some further investigating in this [git repo](https://github.com/20k/boost_beast_leak_client/blob/master/main.cpp): The leak does not occur if connections occur sequentially (start -> write -> shutdown; start -> write -> shutdown), no matter how they're shut down (abort vs graceful shutdown). The leak only occurs if there are a large number of active connections simultaneously, and then it does not matter how they are shut down. I'm also in the cpplang slack if you're investigating and want to share notes!

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-10-16 09:38:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-709940712  

I'm in the #beast channel whenever I'm at my desk - which is most of the day in most time zones

---

## Comment 10

> Username: 20k  
> Created at: 2020-10-17 11:38:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2105#issuecomment-710851154  

So: Long story short, this looks essentially like some kind of measurement error with top reporting incorrect memory usage, compared to valgrind and massif's output. Some way to flush the write buffer of a socket to reduce memory usage while there are a large number of concurrent connections when compression is enabled would be helpful, but this looks like a non bug essentially
