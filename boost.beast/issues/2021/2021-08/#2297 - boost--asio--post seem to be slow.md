# #2297 - boost::asio::post seem to be slow [Closed]

> Username: luxapana  
> Created at: 2021-08-11 16:23:06 UTC  
> Updated at: 2024-10-21 17:12:02 UTC  
> Closed at: 2022-06-16 14:33:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2297  

I am using boost beast as a http client. I run an event loop with a single thread by calling net::io_context::run. A different thread calls boost::asio::post() with the above io_context object and a lambda which handles http request. boost::asio::post is called for each and every outgoing request.   
The performance of this seem to be very low. A valgrind profile run revealed that lots of time is spent on methods such as  'boost::asio::detail::scheduler::post_immediate_completion(boost::asio::detail::scheduler_operation*, bool)'.  
  
It looks like I am not using the library correctly. What is the recommended approach to do this?  
  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-08-11 16:23:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-896968366  

> It looks like I am not using the library correctly. What is the recommended approach to do this?  
  
Have you had a look at the HTTP examples, here?  
https://github.com/boostorg/beast/tree/710cc53331f197f6f17e8c38454c09df68e43c03/example/http

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-08-11 17:34:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897017536  

Have you compiled a release build? The amount of code generated is vastly different.  
  
furthermore, are you inadvertently blocking on async operations?

---

## Comment 3

> Username: luxapana  
> Created at: 2021-08-11 17:38:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897019970  

above code is from a debug build, but with a release build with 03 optimization I see similar pattern.   
My implementation is similar to the session class found in examples which use only async operations. What do you mean by blocking in async operations?

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-08-11 17:40:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897020883  

I mean that asio and beast offer demonstrably good performance.  
My suspicion is that there is an inadvertent wait state in your code.

---

## Comment 5

> Username: luxapana  
> Created at: 2021-08-11 17:41:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897021406  

@vinniefalco I checked most of the async examples but could not find an example that matches my requirements.  
In my case, I have a dedicated thread that runs the event loop and other threads need to post http send requests to the event loop thread.

---

## Comment 6

> Username: luxapana  
> Created at: 2021-08-11 17:43:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897022729  

@madmongo1 my code is very similar to async examples.   
async_resolve -> on_resolve -> async_connect -> on_connect -> async_write -> on_write -> async_read -> on_read

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-08-11 17:44:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897023513  

Which operation in particular is measurably slow?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-08-11 17:49:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897026568  

Are you using SSL?

---

## Comment 9

> Username: luxapana  
> Created at: 2021-08-11 17:56:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897031285  

Boost::asio::post  
  
It seem to allocate memory through new operator.  I am not sure why it  
needs to do that. I will attach the valgrind output of the release build  
tomorrow.  
  
Thanks for the support.  
  
On Wed, 11 Aug 2021, 23:14 Richard Hodges, ***@***.***> wrote:  
  
> Which operation in particular is measurably slow?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2297#issuecomment-897023513>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AK4QRXV262O74FRM5GEPXXTT4KZITANCNFSM5B65LISA>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&utm_campaign=notification-email>  
> .  
>

---

## Comment 10

> Username: luxapana  
> Created at: 2021-08-11 17:56:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897031507  

No I do not use SSL.  
  
On Wed, 11 Aug 2021, 23:19 Vinnie Falco, ***@***.***> wrote:  
  
> Are you using SSL?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2297#issuecomment-897026568>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AK4QRXSQMCSGFWKKQX3UUQDT4KZ2BANCNFSM5B65LISA>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&utm_campaign=notification-email>  
> .  
>

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-08-11 17:59:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897033601  

Asio will allocate a block of memory once per asynchronous operation chain. It will then reuse that memory for each subsequent operation in the chain.  
  
this is not your performance problem.  
  
mid you are noticing (with the human eye) any kind of delay, it is not because of the use of new.  
  
it is because your program has stalled while waiting for something to happen.

---

## Comment 12

> Username: luxapana  
> Created at: 2021-08-12 03:24:14 UTC  
> Updated at: 2021-08-12 03:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897318235  

Actually, this handles 2200 http requests per second with a single thread. I am using uWebSockets for the server part of the same program which handle 4000-5000 requests per second with SSL.  
This essentially make the http client part the bottleneck of the program. I can assure that non of the threads are waiting for anything except two networking threads. (probably in epoll).  
However in my code, I am re-using the http connection. so the lifetime of an http connection object is:  
get constructed -> resolve -> connect -> connected.  
  
once connected I do not call async resolve/connect for future requests.  
  
I let simultaneous async read and async write operations to run, but serialize async writes and async reads individually. That is at any given moment only one asyn_read or async_write is in progress.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2021-08-12 04:43:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897344535  

uWebSockets is pretty fast. But HTTP is different from WebSocket. Please try benchmarking the Beast HTTP examples using a single thread for comparison.

---

## Comment 14

> Username: luxapana  
> Created at: 2021-08-12 05:28:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897359738  

Does this mean 2200 http request/response per second with one io_context thread is reasonable? This is ok as I can add more threads. But I expected a single thread to handle more than 2200 with o3 optimized build.

---

## Comment 15

> Username: luxapana  
> Created at: 2021-08-12 05:36:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897362407  

Please see the kcachegrind view of the call chain here:  
[https://ibb.co/NmwY5v3](url)  
thread_info_base::allocate accounts for around 41% and scheduler::post_immediate_completion takes 48%.  
  
Given that I only use a single thread to run the io_context loop, can these mutex locks/unlocks be avoided?

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-08-12 06:22:01 UTC  
> Updated at: 2021-08-12 06:22:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-897380382  

What would be interesting to see would be the total time that the thread is idle while waiting on IO as a proportion of each time period (I.e. in each 1-second window how long are we just waiting for the network layer?)  
Http is a half-duplex protocol. Websocket, whether you use beast’s implementation or uWebsockets, is full duplex. Network latency and the responsiveness of your web server will matter.  
  
If your code really is spending 48% of its time posting completion handlers then either:  
A) your network and web server are blindingly fast, or  
B) there’s something fundamentally wrong with your client.  
  
Taking an uncontended mutex boils down to one simple atomic exchange. It is (should be) so fast as to be unmeasurable.  
  
Please create a minimal complete verifiable example of just the problematic  Http part of your code so that I can test locally. The need here it to isolate just the http code so that we can prove that this is the problem and exactly which part of it is the problem.

---

## Comment 17

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-1008220747  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: pfeatherstone  
> Created at: 2024-10-03 08:09:45 UTC  
> Updated at: 2024-10-21 17:12:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-2390788308  

Presumably boost::asio::io_context uses some kind of thread safe queue internally for enqueueing work. Has anyone ever benchmarked that queue? I've got a low latency application running and I'm also finding that boost:asio::post seems to have non-negligible overhead. There was a [talk by Sean Parent](https://youtu.be/zULU6Hhp42w?t=2444) where he benchmarked boost::asio::io_context to see if he could use it for a tasking system and it was pretty slow. I know he's interested in CPU-bound tasking, which Asio isn't designed for but still. In my case, I'm reading audio off an ADC and sending it over UDP using Asio. Asio sometimes struggles to keep up as I have lots of small audio buffers to keep latency down.

---

## Comment 19

> Username: ashtum  
> Created at: 2024-10-03 08:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-2390871826  

> Presumably boost::asio::io_context uses some kind of thread safe queue internally for enqueueing work. Has anyone every benchmarked that queue? I've got a low latency application running and I'm also finding that boost:asio::post seems to have non-negligible overhead. There was a [talk by Sean Parent](https://youtu.be/zULU6Hhp42w?t=2444) where he benchmarked boost::asio::io_context to see if he could use it for a tasking system and it was pretty slow. I know he's interested in CPU-bound tasking, which Asio isn't designed for but still. In my case, I'm reading audio off an ADC and sending it over UDP using Asio. Asio sometimes struggles to keep up as I have lots of small audio buffers to keep latency down.  
  
If your application is single-threaded, you might want to use `BOOST_ASIO_CONCURRENCY_HINT_UNSAFE` when constructing the `io_context`.  
Another option would be using `asio::experimental::concurrent_channel` for passing values (with `try_send` and `try_receive_n`). This approach reduces the need to schedule many operations on the scheduler.

---

## Comment 20

> Username: pfeatherstone  
> Created at: 2024-10-03 09:07:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-2390904436  

@ashtum Thank you for your response. Yes it's single threaded, at least the networking IO side of things, and I use the concurrency hint `boost::asio::io_context ioc{1};`. I've never used `asio::experimental::concurrent_channel`. I'm not quite sure what it solves in this case. I read audio data from ALSA on another thread, then I post on the UDP socket's strand which will package up then send. How would a concurrent_channel help here? Cheers

---

## Comment 21

> Username: pfeatherstone  
> Created at: 2024-10-03 09:09:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-2390907979  

I was wondering if the internal queue inside in `io_context` could do with some optimizing, maybe using some lock-free mechanism. I appreciate that in general lock-free isn't helpful for IO, but I dunno, it just seems slow.

---

## Comment 22

> Username: ashtum  
> Created at: 2024-10-03 09:17:33 UTC  
> Updated at: 2024-10-03 09:23:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-2390923040  

> @ashtum Thank you for your response. Yes it's single threaded, at least the networking IO side of things, and I use the concurrency hint `boost::asio::io_context ioc{1};`.   
  
Please note that if you are posting from another thread, you cannot use `BOOST_ASIO_CONCURRENCY_HINT_UNSAFE`.  
  
> I've never used `asio::experimental::concurrent_channel`. I'm not quite sure what it solves in this case. I read audio data from ALSA on another thread, then I post on the UDP socket's strand which will package up then send. How would a concurrent_channel help here? Cheers  
  
Currently, it seems you're performing a `post` for each value you read, which causes significant contention on the internal mutex in Asio. By using a current channel with `try_send` and `try_receive_n`, it would only post when the receive side is waiting; otherwise, it would be a lightweight enqueue operation.  
  
Related to this topic: [Boost Asio experimental channel poor performance](https://stackoverflow.com/questions/73505015/boost-asio-experimental-channel-poor-performance)

---

## Comment 23

> Username: pfeatherstone  
> Created at: 2024-10-03 09:27:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-2390941163  

@ashtum Even though there is only 1 thread running `boost::asio::io_context::run()`, i.e. the main thread, if external threads post, then you can't use `boost::asio::io_context ioc{1}` ? I didn't know that. Thank you. I thought the concurrency hint related to how many threads were running `boost::asio::io_context::run()`...  
  
If I were to use `concurrent_channel`, if `try_send` failed, I would have to buffer somewhere right ? I would have to check my architecture, but maybe I would have to buffer on the thread managing the ADC.

---

## Comment 24

> Username: ashtum  
> Created at: 2024-10-03 09:39:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2297#issuecomment-2390966436  

> @ashtum Even though there is only 1 thread running `boost::asio::io_context::run()`, i.e. the main thread, if external threads post, then you can't use `boost::asio::io_context ioc{1}` ? I didn't know that. Thank you. I thought the concurrency hint related to how many threads were running `boost::asio::io_context::run()`...  
  
`boost::asio::io_context ioc{1}` is different from `BOOST_ASIO_CONCURRENCY_HINT_UNSAFE`. What you're doing is safe, I wanted to point out that you cannot use `BOOST_ASIO_CONCURRENCY_HINT_UNSAFE`.  
  
> If I were to use `concurrent_channel`, if `try_send` failed, I would have to buffer somewhere right ? I would have to check my architecture, but maybe I would have to buffer on the thread managing the ADC.  
  
You assign a size to the channel (via the constructor), and that will serve as your limit. The same applies to ASIO's internal queue when performing `post` operations, it needs to queue them somewhere. So, the send side should be faster otherwise there is no way your application work correctly.
