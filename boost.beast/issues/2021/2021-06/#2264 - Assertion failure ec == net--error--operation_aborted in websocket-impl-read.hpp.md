# #2264 - Assertion failure ec == net::error::operation_aborted in websocket/impl/read.hpp [Closed]

> Username: mologie  
> Created at: 2021-06-20 15:41:03 UTC  
> Updated at: 2022-09-01 21:41:27 UTC  
> Closed at: 2022-09-01 15:42:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2264  

Hi, I am hitting the following assertion with a websocket client:  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/read.hpp#L126  
  
Looking at the called function `check_stop_now` it seems it can return a variety of errors, a timeout in my case. The BOOST_ASSERT macro that I am hitting makes no sense to me / I see no reason why only the operation aborted code would be expected at that place.  
  
### Version of Beast  
  
313 (relevant code available in master too)  
  
### Steps necessary to reproduce the problem  
  
I am having trouble extracting a minimal example due to dependencies of my application and the timeout use case requiring a suitable backend.  
  
Hence this ticket is primarily to assure myself that this indeed /could/ be an issue in Beast.  
  
Please let me know your thoughts so far and whether the assertion is plausible. I will gladly invest the time to build an example unless it's something obvious and none is needed. Thank you!

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-20 17:43:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-864588517  

This is a common occurrence when user programs do not respect the preconditions.  
Remember that streams are not thread safe and you may only have one async write operation in progress at any one time.

---

## Comment 2

> Username: mologie  
> Created at: 2021-06-20 18:11:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-864591802  

I believe that I meet the preconditions, but if you say that this is a common occurrence I will try to reduce it to a minimal example.  
  
It would be great if you could have a short look at the assertion nevertheless, I cannot make heads or tails of it and it is the only place where it compares against operation_aborted after calling into `check_stop_now`.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-06-21 10:48:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-864934090  

If you can send me a minimal, complete example (i.e. a 1-file program that demonstrates the problem) then I'm happy to debug it.  
  
What we normally find is that when people go through this exercise, it uncovers a logic error in their program.  
  
If you can repeat the problem in the MCVE then I'm more than happy to devote time to it.

---

## Comment 4

> Username: ddevienne  
> Created at: 2021-06-21 12:15:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-864985584  

If this is as common, then having an _counter_ example that demonstrates doing it _wrong_, explaining **why** it's wrong, with a fixed version (as separate file to diff with, or in a single file with #if to alternate between GOOD and BAD versions), might be very valuable. I've followed Beast for a long time, and read the whole doc and example code at least twice, and I've never quite read anything as obvious as   
  
> you may only have one async write operation in progress at any one time  
  
PS: Still on WebSocketPP, I'm ashamed of saying, for my _main_ server... Using Beast for other smaller servers, but that comment scares me a little, I must admit, since I planned I try to migrate to Beast for that main server. WebSocketPP's on_message() callback appears to be serialized, after which I post to a cascade of serial strands to have an async processing pipeline, and also to free up the _WebSocketPP socket strand_ so that sending messages does not just queue-up, but are actually sent, so sounds like Beast would not be a straighforward as I hoped for :(

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-06-21 12:53:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-865008433  

Hi there, I've just had a quick look through the documentation to be sure we had not forgotten anything.  
  
Please see for example:  
  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html  
  
> The program must ensure that no other calls to write, write_some, async_write, or async_write_some are performed until this operation completes.

---

## Comment 6

> Username: mologie  
> Created at: 2021-06-21 12:58:34 UTC  
> Updated at: 2021-06-21 12:59:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-865011318  

Thank you both for your comments. So far I have not found a precondition in the docs that I violate, and have determined that the problem only occurs when I configure the timeout option on the websocket prior to beginning the handshake. This is not the default recommended by the docs. The recommended client values as described in the docs do not get me the error behavior.  
  
The timeout option supports what I observe in Beast's source code: It appear to introduce an error code where another is expected, yet it is /very/ tricky to reach it. I have yet to produce a minimal example and will probably check back in by the end of the week. I shall close the ticket if I do not manage to reproduce independently by then.  
  
Reproducing the bug right now requires my iOS test app and turning WiFi off when it tells you to, but that does not make for a nice unit test ;-).

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-06-21 13:36:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-865039487  

If it doesn't happen in an MCVE, or in any of our test programs, then it's likely to be a bug somewhere else. Common errors are:  
  
- more than one async_write in progress at once (failure to implement a write queue)  
- two threads accessing the websocket concurrently (non-use of a strand in a multi-threaded program)  
- destroying the websocket stream while there is an outstanding async read or write in progress. Note that this is allowed for an Asio socket, but not for a beast websocket stream. Beast's asynchronous operations are _composed operations_. They touch the websocket stream's state multiple times before the completion handler is executed.  
- For the above reason, it's very important that both async_read, async_write and async_close are all initiated from the same strand, whether implicit or explicit.  
-

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-06-25 06:55:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-868270103  

Are we in a position to close this issue now?

---

## Comment 9

> Username: imerr  
> Created at: 2021-09-13 12:11:00 UTC  
> Updated at: 2021-09-13 12:23:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-918128725  

I've just had a user report about triggering this exact assertion failure under a DOS condition (connection spam & "Too many open files" in accept)  
I *believe* I'm handling this properly as well, single thread, strands (can be configured to use multiple threads, but I can reproduce the crash with one) and only one async_read waiting for data (which never arrives since the connections are just opened and idle), no writes until a read happens  
  
> destroying the websocket stream while there is an outstanding async read or write in progress. Note that this is allowed for an Asio socket, but not for a beast websocket stream. Beast's asynchronous operations are composed operations. They touch the websocket stream's state multiple times before the completion handler is executed.  
  
As in having the websocket stream object get destroyed? I'm using the shared_ptr capture technique from the examples to keep the object alive while there are operations pending  
  
I seem have worked around it for now by not using the built-in websocket timeouts, the crash is unfortunately a bit random to trigger.  
  
I've also tried to reproduce this with the async websocket example server without any luck so far.  
  
Could one of you please explain why the timeout error "returned" by check_stop_now is not expected to happen in the read some operation? I've done some digging myself, but unfortunately it's not really clear to me, maybe a comment would be helpful there?  
https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/include/boost/beast/websocket/impl/read.hpp#L124-L128  
https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/include/boost/beast/websocket/impl/stream_impl.hpp#L337-L345

---

## Comment 10

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1008220736  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 11

> Username: imerr  
> Created at: 2022-01-09 03:44:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1008223288  

> This issue has been open for a while with no activity, has it been resolved?  
  
Still would like some clarification if possible

---

## Comment 12

> Username: sky-bro  
> Created at: 2022-05-08 09:23:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1120382540  

> If it doesn't happen in an MCVE, or in any of our test programs, then it's likely to be a bug somewhere else. Common errors are:  
>   
> * more than one async_write in progress at once (failure to implement a write queue)  
> * two threads accessing the websocket concurrently (non-use of a strand in a multi-threaded program)  
> * destroying the websocket stream while there is an outstanding async read or write in progress. Note that this is allowed for an Asio socket, but not for a beast websocket stream. Beast's asynchronous operations are _composed operations_. They touch the websocket stream's state multiple times before the completion handler is executed.  
> * For the above reason, it's very important that both async_read, async_write and async_close are all initiated from the same strand, whether implicit or explicit.  
  
can I call async_close while there's an ongoing async_read or async_write?  
  
how should I close the websocket correctly in this case.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-05-08 15:49:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1120442140  

> can I call async_close while there's an ongoing async_read or async_write?  
  
Yes

---

## Comment 14

> Username: gopalak  
> Created at: 2022-08-31 13:24:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1232935404  

@vinniefalco @madmongo1 and the OP - I saw this happen in our Prod env under load. (@vinniefalco and @madmongo1 ) this is after I change tcp::stream to tcp::socket as for my websocket stream declartion.  
So from the core and logs this is the sequence that I could deduce. Its kind of a tricky one and I dont have a reproducible one page code or any thing at this time   
  
Consider a server which is accepting SSL WSS connecions  
  
My client which crashed -> starts a new connectiom, SSL handshake done, WS handshake done  
I have a timer for read and write operatons - send some data  
read timer started, no data comes   
SERVER is in a state where its crashing or stuck -   
Read timer fires  
I call async_close on the websocket stream  
  
This is where it gets interesting   
1. Instead of the stream being closed (which happens for most cases)  
2. the one where it ABORTS, the async_close came back with OP Canceled -  
3. This async_close also internally has sent an OP Canceled to the pending ASYNC read  
4. That is where the assert was triggered where the beast code is expected an ec OP_ABORTED instead of CANCELED  
  
This is not some thing which happens normall. I usually see an async_close succeed and the async read gets cleared correctly. The above scenario IMO should possibly be handled where the async read should expect either an OP ABORTED or OP CANCELED based on what I see in this case.  
  
Any thoughts comments ? I can add a timer for tracking the async close as well so I can catch it and call the under lying socket->cancel to avoid this ABRT I think. But the question is this scenario can happen in many situations depending on how the async close is served due to server mis behavior or network issues etc.  
  
In my case the async_close took 30 seconds to time out and return an op canceled - let me know if u need more info  
Hope this scenario can be addressed in the code path than just ABORT

---

## Comment 15

> Username: madmongo1  
> Created at: 2022-08-31 13:57:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1232975069  

I'd be interested to see the minimal code that implements this. I do a similar thing (including a timeout to close the underlying socket if the async_close does not complete in time). I have not had any problems so far.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2022-08-31 14:04:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1232984379  

I don't think a code example is going to be particularly helpful unless you manually advance the `io_context` and force this to happen synthetically (which is what you would need to do in order to write a deterministic unit test). However, from looking at the code I believe this line is incorrect  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/read.hpp#L126  
there is no guarantee to see `operation_aborted`. It is always possible to have a rare case where the connection is reset by the peer at the exact same time that the timer would be canceled. In this case there is no guarantee that we will see the `operation_aborted` first. This assert should be removed.

---

## Comment 17

> Username: gopalak  
> Created at: 2022-08-31 14:04:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1232984442  

> I'd be interested to see the minimal code that implements this. I do a similar thing (including a timeout to close the underlying socket if the async_close does not complete in time). I have not had any problems so far.  
  
I dont have a timer at this time for async_close - it seems to be a default 30 sec timer at network layer. With that I don't hav a cancel call - is this mandatory or expected ? as the doc doesnt ask for an underlying socket cancel call to be called if async_close is called. Shouldnt the beast code be handling the above scenario and if not may be we need some extra doc to explain the above  
As I said this doesnt happen all times. It seems to be when the specific window is hit and the beast websocket read code asserts expecting operation_aborted but I think it gets canceled as the ec value

---

## Comment 18

> Username: gopalak  
> Created at: 2022-08-31 14:05:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1232985770  

> I don't think a code example is going to be particularly helpful unless you manually advance the `io_context` and force this to happen synthetically (which is what you would need to do in order to write a deterministic unit test). However, from looking at the code I believe this line is incorrect https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/read.hpp#L126 there is no guarantee to see `operation_aborted`. It is always possible to have a rare case where the connection is reset by the peer at the exact same time that the timer would be canceled. In this case there is no guarantee that we will see the `operation_aborted` first. This assert should be removed.  
  
Thank you @vinniefalco exactly my point - we cant expect the operation aborted there was what I was getting at

---

## Comment 19

> Username: gopalak  
> Created at: 2022-08-31 14:08:18 UTC  
> Updated at: 2022-08-31 14:10:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1232988951  

@vinniefalco @madmongo1 let me know which version this will be fixed ? So I can update to that - appreciate the help. I am on beast version 306 from version.hpp

---

## Comment 20

> Username: gopalak  
> Created at: 2022-09-01 04:08:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1233713474  

@vinniefalco @madmongo1 let me know if u want me to file an issue for this as well

---

## Comment 21

> Username: madmongo1  
> Created at: 2022-09-01 08:07:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1233905351  

We can work with this issue. I'll post a fix as soon as I get a moment.

---

## Comment 22

> Username: imerr  
> Created at: 2022-09-01 08:29:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1233933070  

Well done @gopalak for figuring this one out 👍

---

## Comment 23

> Username: gopalak  
> Created at: 2022-09-01 14:17:58 UTC  
> Updated at: 2022-09-01 14:18:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1234347560  

@madmongo1 Thanks for the quick turn around. how do I get this patch ? I am on boost 1.75 and beast version 306 ?  
or do you want me to switch boost 1.80 and its beast version there and apply the patch ?

---

## Comment 24

> Username: madmongo1  
> Created at: 2022-09-01 15:37:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1234452792  

If the tests pass I'll merge it now

---

## Comment 25

> Username: madmongo1  
> Created at: 2022-09-01 15:46:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1234463980  

```  
cd <boost source dir>  
git checkout master -u origin/master  
git pull  
git submodule update --init --recursive  
(cd libs/beast && git  checkout master && git pull)  
./bootstrap  
./b2 ... etc  
```

---

## Comment 26

> Username: gopalak  
> Created at: 2022-09-01 19:56:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1234716461  

Thanks @madmongo1 - so I did a pull and see the code change. I did a bootstrap.sh with --prefix option  
and when I run b2 ... install --> the change doesnt get installed ? Am I missing some thing here ? for options to be pased for b2 to do install ?

---

## Comment 27

> Username: madmongo1  
> Created at: 2022-09-01 21:41:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2264#issuecomment-1234811509  

```  
$ ./b2 --prefix=<where you want boost installed> variant=release [any other options that matter to you] install  
```
