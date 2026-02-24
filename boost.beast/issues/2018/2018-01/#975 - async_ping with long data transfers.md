# #975 - async_ping with long data transfers [Closed]

> Username: rberlich  
> Created at: 2018-01-10 15:14:16 UTC  
> Updated at: 2018-01-11 03:15:09 UTC  
> Closed at: 2018-01-10 18:45:16 UTC  
> Url: https://github.com/boostorg/beast/issues/975  

Hi there,  
  
short question: what would be a good strategy to send an async_ping in the presence of very long data transfers (longer than the interval between two pings)?  
  
Or, somewhat longer / TLDR: I'm trying to understand what happens to a ws.async_ping when a concurrent data transfer takes place. Sending both through a strand should result in the ping being queued so that, for very long data transfers (e.g. due to some very slow connection) the ping might be delayed beyond the ping interval. The originator of the ping might then "think" that there was no response and terminate the connection.  
  
ws.async_write() is, according to the [docs](http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html) , implemented via asio::async_write_some() . So when longer transfers are divided into smaller portions, it might be possible to send queued control frames in the completion handler of async_write_some() .  
  
This might however require the receiving end to constantly monitor the stream for control frames and "cut them out" of the "real" payload, which sounds like it may need a lot of compute time just to "catch" the occasional control frame.   
  
Then again, auto-fragmentation is turned on by default, so that the queue might actually see multiple messages for one payload and the originator may be able to "squeeze in" the control frame? This would however only help if the sending queue had some sort of a priority setting, so that control frames would be handled with preference.  
  
So I am confused a bit and would like to ask what the best strategy for sending/receiving pings and pongs would be in the presence of async_write/read calls with a lot of data.  
  
This is, admittedly an edge case, as the normal interval between two pings will be >= 10 s; still such long data transfers may be possible. What would be a good strategy here?  
  
Thanks and Kind Regards,  
Beet

---

## Comment 1

> Username: rberlich  
> Created at: 2018-01-10 15:59:12 UTC  
> Updated at: 2018-01-10 16:05:32 UTC  
> Url: https://github.com/boostorg/beast/issues/975#issuecomment-356646300  

Apologies for responding to my own message. One way of dealing with long writes and pings could just be to implement them in terms of [ws.async_write_some](http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html). So whenever the handler returns without error code, the connection is clearly alive and some flag could indicate to the async_wait completion handler from the ping process, that despite a timeout without control frame the connection is still alive. While no write-operation takes place, the normal ping will take the role of ensuring the integrity of the connection. Of course this takes away some of the fun, as ws.async_write() is quite convenient.   
  
Another option would be to initiate a ping-session from both sides. As (at least my) protocol does not involve concurrent reads/writes (except for the ping), arriving pings may still be registered during a long write-operation, even though the own pong might not leave the system until after the write. A similar rationale applies to long read-operations. A concurrent ping (i.e. a write-operation) could still leave the system, and if the handler shows no error, the connection is still alive.   
  
So if there is a better option for long writes involving ws.async_write() alone, any feedback would be appreciated.  
  
Thanks and Kind Regards,  
Beet

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-01-10 16:28:22 UTC  
> Url: https://github.com/boostorg/beast/issues/975#issuecomment-356656120  

You're way over-thinking this :) Just set `auto_fragment(true)` and call it a day!  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/control_frames.html#beast.using_websocket.control_frames.auto_fragment

---

## Comment 3

> Username: rberlich  
> Created at: 2018-01-10 18:45:16 UTC  
> Url: https://github.com/boostorg/beast/issues/975#issuecomment-356697617  

Thanks -- this helps a lot (as always)!  
  
Small typo im the "Close Frames"-section: "When a close frame is received by during a read operation" --> "... by during ..."  
  
Thanks,  
Ruediger

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-11 03:15:09 UTC  
> Url: https://github.com/boostorg/beast/issues/975#issuecomment-356811792  

The **master** branch versions of **advanced-server** and **advanced-server-flex** demonstrate how to use pings and pongs efficiently to check for a lively host, I suggest taking a look.
