# #986 - Estray / Testbed for the integration of Beast and Boost.Serialization [Closed]

> Username: rberlich  
> Created at: 2018-01-16 18:19:37 UTC  
> Updated at: 2018-05-31 20:53:32 UTC  
> Closed at: 2018-05-31 20:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/986  

Hi there,   
  
I have spent some time to find my way around the websocket part of Boost.Beast, specifically with the goal of creating a testbed for my own needs -- a client-server framework in the area of distributed parametric optimization. As I believe that the code may be useful for others as well, I have made this testbed available here on Github, see [Estray](https://github.com/rberlich/Estray).  
  
Estray constantly creates payload objects in the server, which are serialized and shipped to clients upon request. Processed items are shipped back to the server, so it implements a simple protocol on top of Beast. It has been tested with up to 500 clients on Linux (running on a small number of machines connected via a fast network, but not in the same data center) and is configurable to simulate different payload types.  
  
There will be many areas in the code that you might consider "crude". Flamewars are welcome as long as you make your criticism constructive :-)   
  
Hope that helps someone.   
  
Kind Regards,  
Beet

---

## Comment 1

> Username: rberlich  
> Created at: 2018-01-27 14:03:55 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-360986866  

Hi there,  
  
the use-cases modeled in Estray may involve long periods of processing on the client side, without any interaction with the server. The implemented protocol is serial in the sense that server-session responses are always triggered by client requests, so that there may not be more than one simultaneous, user-initiated read- or write.   
  
However, in order to answer server-session pings. a read-session must always be active on the client-side, i.e. the completion-handler of ws.async_read() will initiate the next async_read(), and needs to trigger asynchronous processing of the incoming server message (and possibly workload).  
  
Where processing of incoming server-messages is done via an io_context::post-call on the client side, as it is currently implemented in Estray (the function supplied to post() will then send / async_write its answer to the server when processing is completed), io_context::run() must be started in two threads (or else the one thread will be blocked by processing).   
  
Hence there is no implicit strand on the client side, which might protect concurrent writes of the process() call and the pong sent back to the server.   
  
This seems to work reliably with 100 clients over many thousand of work packages sent back and forth, but that is not the same as "bug-free".  
  
***  
So do I need to synchronise pongs and writes that may happen from a different thread on the client side?  
***  
  
It is my understanding that the concurrent read- and write-access to the socket by the continuously running async_read and the async_write triggered by the processing call from a different thread are safe.  
  
In case you want to try it out:  
  
Both client and server are implemented in async_websocket_server.hpp/cpp in  [Estray](https://github.com/rberlich/Estray), including a CMake-based build-system.  
  
In order to have a server emitting workloads that will trigger a 20 second "processing" (really a std::this_thread::sleep_for() ) on the client side and a 2 second ping cycle between client and server, with messages whenever a ping/pong is received, on a single Linux-system start the server with  
  
./Estray --payload_type=1 --payload_sleep_time=20 --ping_interval=2 -v  
  
and the client with   
  
./Estray --client --v  
  
Thanks and Kind Regards,  
Beet

---

## Comment 2

> Username: rberlich  
> Created at: 2018-01-27 14:50:36 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-360989657  

O.k., you say [here](http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/control_frames.html) wrt control frames: "A consequence of this automatic behavior is that caller-initiated read operations can cause socket writes. However, these writes will not compete with caller-initiated write operations. For the purposes of correctness with respect to the stream invariants, caller-initiated read operations still only count as a read. This means that callers can have a simultaneously active read, write, and ping/pong operation in progress, while the implementation also automatically handles control frames." . So that would mean that the write initiated by the process()-call on the client side does not interfere with the automatic pong.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-27 14:51:13 UTC  
> Updated at: 2018-01-27 14:51:38 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-360989697  

Yes, that is correct. See also:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety

---

## Comment 4

> Username: rberlich  
> Created at: 2018-01-27 15:18:04 UTC  
> Updated at: 2018-01-27 15:25:23 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-360991443  

O.k., thanks, that has helped me a lot!  
  
I am still confused about the following. You say [here](http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_ping.html) about async_ping: "This operation is implemented in terms of one or more calls to the next layer's async_write_some functions, and is known as a composed operation. The program must ensure that the stream performs no other writes until this operation completes.". So I read this as "never start an async_write() without synchronization, while a ping is being sent".   
  
However, your notes about thread-safety (which I assume also apply to Beast in Boost 1.66 ?) seem to imply that it is absolutely o.k. to start an async_write, while an async_ping is in progress. That would indeed make things much simpler, as it can be quite hard to control when a ping goes out (I guess one would have to cancel the ping timer before initiating an async_write and then restart it in the completion handler).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-01-27 15:27:19 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-360992048  

I agree the documentation is a bit confusing, it is difficult for me to find the right words to explain it. The text you quoted was copied from Asio.  
  
>it is absolutely o.k. to start an async_write, while an async_ping is in progress  
  
Yes, it is OK. As you noted, if Beast did not take care of this it would require users to write a lot of ugly, complicated code which would be difficult to maintain.

---

## Comment 6

> Username: rberlich  
> Created at: 2018-01-27 15:54:48 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-360993818  

Maybe in the async_ping documentation for Boost 1.67 (I guess 1.66 cannot be changed anymore?), just a link to the notes you mention above would suffice (I guess instead of the "The program must ensure that the stream performs no other writes until this operation completes.").  
  
Thanks again for all the hard work you put into Beast (and congratulations to the Bishop Fox Review Beast has passed -- I have just stumbled upon the [interview](https://www.youtube.com/watch?v=4TtyYbGDAj0) you gave on the topic).

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-01-27 16:17:40 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-360995324  

The reference section is generated from Doxygen. It is not possible to link to the other chapters, since those are processed in a separate toolchain.

---

## Comment 8

> Username: rberlich  
> Created at: 2018-01-28 21:02:57 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-361095770  

Two questions:  
  
- I can handle 500 concurrent websocket connections with relatively complex workloads with Beast in my testbed, and I do have the impression (but not the resources to test) that more is possible. I know ASIO can handle many more simultaneous connections. But Websockets add overhead. What limits do you envisage? I.e. -- how many connections would you expect a well equipped server (say a 24 core AMD EPYC with decent connectivity) to be able to handle (not considering computing-intensive tasks such as de-/serialization or bandwidth constraints)? Where does Beast itself stop to scale ?  
  
- Totally off-topic: In your [Bishop-Fox Youtube clip](https://www.youtube.com/watch?v=4TtyYbGDAj0) you are using a Mac, which I assume is your main platform. I was not quite able to recognize the IDE you were using there - my guess is Clion. What do you -- personally -- recommend for C++ development on Linux and Mac ?   
  
Thanks and Kind Regards,  
Beet

---

## Comment 9

> Username: djarek  
> Created at: 2018-01-28 21:43:29 UTC  
> Updated at: 2018-01-28 21:44:24 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-361098760  

@rberlich   
ASIO (and Beast as well) leaves the high-level system architecture up to the user. With a 24 core CPU you might run into contention on locks protecting the internal reactor in ASIO's `io_context`s. Theoretically, from the point of view of Amdahl's Law, you get the best scaling when you have one `io_context` per core, however, you will need to handle load balancing on your own (and if you screw up the load balancing, you will get worse performance than with one `io_context` with all threads in it). Choosing the threading/load-balancing strategy is heavily dependent on your code, so remember to do your research and measure to verify your assumptions.  
  
To sum up, ASIO+Beast itself is definitely good enough to beat C10k on typical server hardware.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-01-28 22:18:49 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-361101394  

>you are using a Mac, which I assume is your main platform  
  
The laptop is for travel and presentations only, I don't do any serious work on it. My main development platform is a Windows 10 gaming desktop with Microsoft Visual Studio.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-01-28 22:23:30 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-361101743  

>Where does Beast itself stop to scale ?  
  
With Beast, it should be possible for you to come close to the maximum theoretical performance possible for whatever your application is. More than anything, the performance limit will depend how you architect your program. It is left to the user to decide if and what optimizations. The example program **http-server-fast** was written by @chriskohlhoff (author of Asio). If you compare this program to the other example servers you can get an idea for what kinds of optimizations are possible. And you can benchmark those programs to compare the performance improvements.

---

## Comment 12

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:35 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-384003570  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: rberlich  
> Created at: 2018-04-24 17:55:49 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-384023657  

Dear Vinnie,  
  
I am not sure whether „issue“ is the right term for Estray. This is just a simple testbed ultimately created to learn something about beast and made public in the hope that it might help other people. So if you want to keep this linked to the Beast github site at all, it might make sense to move this topic to another category (misc, third-party, or something along these lines).  
  
Kind Regards,  
Beet  
  
  
> Am 24.04.2018 um 18:52 schrieb stale[bot] <notifications@github.com>:  
>   
> This issue has been open for a while with no activity, has it been resolved?  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/986#issuecomment-384003570>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ASlWX20qnSC3ucWmq2DRGC2K1EW3kpHOks5tr1hXgaJpZM4RgNt6>.  
>

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-04-24 17:58:23 UTC  
> Updated at: 2018-04-24 17:58:41 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-384024539  

Okay, I've enabled the wiki feature if you would like to create a page for this. See: https://github.com/boostorg/beast/wiki

---

## Comment 15

> Username: rberlich  
> Created at: 2018-04-24 18:06:24 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-384027109  

Great. Do you want to add some categories first to this page ?   
  
If not, I suggest to start simple with a page „Third Party Examples“, with a test "This is a list of examples made available outside of the Beast source tree. Note that these are not maintained by Vinnie or the Beast community and that it cannot be guaranteed that the code is up to date. Please contact the corresponding author if you come across an issue.“, plus a list of external examples (currently just Estray).   
  
 Categories can then be added if and when further pages are added to the Wiki.  
  
O.k. for you ?  
  
Kind Regards,  
Beet  
  
> Am 24.04.2018 um 19:58 schrieb Vinnie Falco <notifications@github.com>:  
>   
> Okay, I've enabled the wiki feature if you would like to create a page for this.  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/986#issuecomment-384024539>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ASlWX3aq4eclJHiw0xGRiQuRBiMElAqfks5tr2fDgaJpZM4RgNt6>.  
>

---

## Comment 16

> Username: vinniefalco  
> Created at: 2018-04-24 19:23:55 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-384050271  

Well a wiki is usually community-run so I think it would be okay for you to edit it as you see fit. We can always adjust it later if we want.

---

## Comment 17

> Username: stale[bot]  
> Created at: 2018-05-24 19:56:30 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-391839349  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: stale[bot]  
> Created at: 2018-05-31 20:53:31 UTC  
> Url: https://github.com/boostorg/beast/issues/986#issuecomment-393676368  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
