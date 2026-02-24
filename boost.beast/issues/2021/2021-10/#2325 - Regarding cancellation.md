# #2325 - Regarding cancellation [Closed]

> Username: Chlorie  
> Created at: 2021-10-09 04:48:13 UTC  
> Updated at: 2022-10-27 14:56:20 UTC  
> Closed at: 2022-10-27 14:56:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2325  

The latest version of the asio library (standalone 1.19 / boost 1.77) now supports per-operation cancellation. Is it possible to integrate this mechanic into beast so that operation cancellation could be more flexible? Thanks!

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-10-09 06:08:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939236096  

It is on my roadmap.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-10-09 11:00:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939277604  

But... what exactly would this do? What operations could we actually cancel that would not leave the stream in an undefined state?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-10-09 15:04:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939311189  

My feeling at the moment is that `terminal` cancellation is trivial (and  
might even just work off the bat).  
It probably won't be too much work to make HTTP operations support  
`partial` cancellation.  
With a bit of work, websocket streams could conceivably support `total`  
cancellation.  
  
  
On Sat, 9 Oct 2021 at 13:01, Vinnie Falco ***@***.***> wrote:  
  
> But... what exactly would this do? What operations could we actually  
> cancel that would not leave the stream in an undefined state?  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2325#issuecomment-939277604>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSPF5ZXOA4JYKDQHUYLUGAOHDANCNFSM5FU3MXXQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-10-09 16:06:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939320358  

websocket streams already support "total cancelation" just close the socket!

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-10-09 19:24:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939349962  

That would be "terminal" cancellation.  
  
"total" means that the cancellation happened without causing observable  
side effects. I believe we can do this in the websocket stream because we  
buffer internally.  
  
  
On Sat, 9 Oct 2021 at 18:06, Vinnie Falco ***@***.***> wrote:  
  
> websocket streams already support "total cancelation" just close the  
> socket!  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2325#issuecomment-939320358>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSPYHQZ6HAOA7KF5T2TUGBSA3ANCNFSM5FU3MXXQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-10-10 00:01:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939378669  

> "total" means that the cancellation happened without causing observable  
side effects.   
  
That is not always possible though, sometimes the stream will be left in an undefined state, sometimes not. Which makes it useless. For example, the user is sending a frame that is not a final frame, and we cancel it. The frame ends up going out. Now what? We are in the middle of reading and we get half of a frame. The user cancels the read. Now what?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-10-10 00:02:42 UTC  
> Updated at: 2021-10-10 00:02:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939378851  

> Is it possible to integrate this mechanic into beast so that operation cancellation could be more flexible?   
  
If we did integrate it, how do you think it will be better than what is already possible right now by either calling `cancel` on the underlying I/O object (`socket` or `ssl_stream`) or by simply closing the socket?

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-10-10 16:22:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939510844  

The default cancellation mode requested is "all" which implies "total |  
partial | terminal", so the implementation is free to implement the one it  
is best able to do and ignore the others if necessary.  
  
So if we were to request a total cancellation of a read and the websocket  
is not in a position to buffer the input gathered so far, it is free to  
ignore the cancellation request.  
  
If it receives a "terminal" request, it should honour this. The simplest  
way to do so is to simply close the underlying stream (assuming there is a  
generic way to do that - streams are not always sockets). Another way to do  
this is to simply ignore the cancellation in which case it will get passed  
on to the next layer automatically. This will in most cases give us what we  
want (any outstanding operations will be interrupted and complete as  
operation_aborted).  
  
So I think the default case is probably automatic, but I also think we can  
do better - assuming there is a use case. The presence of a use case for  
this is by no means proven at this point.  
  
  
On Sun, 10 Oct 2021 at 02:01, Vinnie Falco ***@***.***> wrote:  
  
> "total" means that the cancellation happened without causing observable  
> side effects.  
>  
> That is not always possible though, sometimes the stream will be left in  
> an undefined state, sometimes not. Which makes it useless. For example, the  
> user is sending a frame that is not a final frame, and we cancel it. The  
> frame ends up going out. Now what? We are in the middle of reading and we  
> get half of a frame. The user cancels the read. Now what?  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2325#issuecomment-939378669>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSM2JJ7FBMJCSNFIKPLUGDJVTANCNFSM5FU3MXXQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-10-10 16:40:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939513357  

> if we were to request a total cancellation of a read and the websocket is not in a position to buffer the input gathered so far, it is free to ignore the cancellation request.  
  
  
What good is canceling if the behavior is unpredictable, i.e. sometimes it works and sometimes it doesn't?

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-10-10 18:31:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939530822  

> What good is canceling if the behavior is unpredictable, i.e. sometimes it works and sometimes it doesn't?  
  
This is inescapable in an asynchronous system. I have the feeling that the cancellation requester can be told if the requested cancellation happened but I need to check.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2021-10-10 18:34:47 UTC  
> Updated at: 2021-10-10 18:35:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939531258  

> This is inescapable in an asynchronous system.   
  
Well no, that's not correct. In our case, the failure mode leaves the stream in an undefined state. But it is easy to imagine idempotent asynchronous operations which do not leave the stream in an undefined state on cancellation. For these purposes, cancel makes total sense. Its just that in our particular use-case of HTTP and WebSocket, I don't see a way to provide a reliable cancel. And unreliable cancel; that is, a cancel which sometimes leaves the stream in an undefined state - is completely useless.  
  
I'm interested in hearing from @Chlorie about the intended use-case with Beast.

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-10-10 18:40:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939531975  

I didn’t mean to imply that cancellation can be arbitrarily downgraded.  
The caller is free to request “any kind of cancellation” of course. This is the default.  
But the user is also free to request “only total cancellation will do”, in which case the operation will either be cancelled in a restartable way (no external side effects) or it won’t be cancelled at all.

---

## Comment 13

> Username: Chlorie  
> Created at: 2021-10-11 03:34:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-939651537  

Sorry, I'm not so familiar with the internal implementation of beast. As @madmongo1 said, asio has support for 3 different types of cancellation, and if the requested cancellation type is not available for the operation, the cancellation is ignored (not downgraded). Even if all of the operations can only support terminal cancellation (by just closing the socket etc.) it is still helpful to make it work with the new cancellation system in asio.

---

## Comment 14

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1008220718  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 15

> Username: mika-fischer  
> Created at: 2022-07-28 14:24:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198212698  

I could use this functionality to cancel a pending `async_read` operation on a `websocket`. I'm not sure what the issue is in this case, that this cannot be implemented reliably.  
  
The reason I need cancelation is lifetimes with coroutines:  
- coro A does the WS handshake and owns the websocket  
- coro A then calls coro B in order to do something with the websocket  
- coro B creates:  
  - an input queue and and output queue  
  - a read coro, which reads from the ws and forwards on input queue  
  - a write coro, which reads from output queue and writes to ws  
  - a worker thread that uses the queues  
- coro B then waits for this worker thread to exit (with success or exception).  
- At this point I want to return to coro A forwarding the exception or success of the worker function and let it handle all the shutdown logic.  
  
And here the issue is that I have no way to "join" the read coro because it is stuck on an `async_read` without support for cancellation.  Note that I explicitly don't want to close the ws here, because coro A already has all the code to translate `std::exception`s to `close_code`s, `close_reasons` etc.

---

## Comment 16

> Username: madmongo1  
> Created at: 2022-07-28 14:33:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198223640  

For the timebeing, you can call `shutdown()` or `close()` on the underlying socket.  
This will cause the async_read to complete with an error.  
Another way to stop in a more controlled way, you can initiate an async_close() on the websocket which the async_read() is still outstanding.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2022-07-28 14:40:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198234661  

can't you call `cancel` on the underlying socket?  
https://www.boost.org/doc/libs/1_79_0/doc/html/boost_asio/reference/basic_stream_socket/cancel.html

---

## Comment 18

> Username: mika-fischer  
> Created at: 2022-07-28 14:45:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198242950  

Thanks for the suggestion @madmongo1, but I know all this. In all the suggested cases I cannot use the shutdown logic already implemented in coro A. I would need to duplicate it or refactor.  
  
I want to call `async_close` in coro A with proper `close_reason` etc. But at this point coro B does not exist anymore, and the read coro references local variables of coro B. I can work around all this in one way or another. But the only clean way I see is to have a way to cancel the pending read *without* closing the socket/websocket. Then coro B could make sure the read coro is done and return safely.  
  
@vinniefalco `cancel` would be an option, I forgot about that and only remembered `close` (which I don't want due to the reasons mentioned above). However the doc comments don't exactly inspire confidence: "  
- It will only cancel asynchronous operations that were initiated in the current thread.  
- It can appear to complete without error, but the request to cancel the unfinished operations may be silently ignored by the operating system. Whether it works or not seems to depend on the drivers that are installed."  
  
So I'm reluctant to rely on this. I need the cancelation to be reliable.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2022-07-28 17:30:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198437510  

> I need the cancelation to be reliable.  
  
Well, if I add the cancellation hook to Beast, the only thing I can do is call `cancel` on the underlying socket the same as you - so it would be as reliable or as unreliable as that. There's no magic bullet here, cancellation of I/O operations is fundamentally a feature of the operating system and not the networking library.

---

## Comment 20

> Username: mika-fischer  
> Created at: 2022-07-28 17:50:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198454791  

I would suggest you just forward the cancellation_slot associated with the user-supplied completion handler to the underlying stream operation and handle the possible error::operation_aborted as appropriate if necessary or just let it pass up to the completion handler if possible. That's really all that's needed unless I'm missing something. I mean these operations can fail with all kinds of errors already, so I'm not sure what makes the cancellation case more difficult to handle.   
  
In any case, cancellation support that would always explicitly call cancel on the underlying socket is better than nothing. Even though I think it's possible and desirable to do better for websockets. I agree that it's probably good enough for HTTP.  
  
Another reason beast should support cancellation is that otherwise it's impossible to use the new asio facilities such as parallel_group, promises and awaitable combination operators. They will just never complete because they inherently rely on cancellation.

---

## Comment 21

> Username: madmongo1  
> Created at: 2022-07-28 17:58:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198460990  

It's not quite that simple. During an async_read operation, quite a few underlying async sub-operations can happen, including reading and writing of control frames and continuation of write and close operations.  
  
It is possible to implement terminal cancellation without a huge amount of difficulty, but this gives you nothing more than you already have by closing the underlying socket, since that is also terminal.  
  
Implementing total (i.e. restartable transactions) cancelation is a major undertaking which will require design decisions and perhaps the use of extra, arbitrarily large buffers.

---

## Comment 22

> Username: vinniefalco  
> Created at: 2022-07-28 18:12:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1198480305  

The problem is that beast does not have a full-time maintainer now and I am working hard on Boost.URL and a few other new libraries built on that. In fact we forgot to merge develop to master for this Boost release cycle, so Beast got no commits for 1.80.0 (which sucks).

---

## Comment 23

> Username: mika-fischer  
> Created at: 2022-07-29 10:20:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1199112321  

> It's not quite that simple. During an async_read operation, quite a few underlying async sub-operations can happen, including reading and writing of control frames and continuation of write and close operations.  
  
Fair enough, this complicates things of course.  
  
> It is possible to implement terminal cancellation without a huge amount of difficulty, but this gives you nothing more than you already have by closing the underlying socket, since that is also terminal.  
  
Yes, but as mentioned above, it would still be worthwhile, because then beast operations would be usable out-of-the-box with asio::promise, asio::parallel_group and so on, which they are not currently.  
  
> Implementing total (i.e. restartable transactions) cancelation is a major undertaking which will require design decisions and perhaps the use of extra, arbitrarily large buffers.  
  
Yes, understood, also not sure if it's worth it...  
  
> The problem is that beast does not have a full-time maintainer now and I am working hard on Boost.URL and a few other new libraries built on that. In fact we forgot to merge develop to master for this Boost release cycle, so Beast got no commits for 1.80.0 (which sucks).  
  
Agreed, that both sucks...

---

## Comment 24

> Username: madmongo1  
> Created at: 2022-07-29 20:55:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2325#issuecomment-1199939152  

>> It is possible to implement terminal cancellation without a huge amount of difficulty, but this gives you nothing more than you already have by closing the underlying socket, since that is also terminal.  
  
>Yes, but as mentioned above, it would still be worthwhile, because then beast operations would be usable out-of-the-box with asio::promise, asio::parallel_group and so on, which they are not currently.  
  
I think this is a fair point.  
It would also impact any in-progress write or async_close. But that's no worse than what we have.  
I agree with your position.
