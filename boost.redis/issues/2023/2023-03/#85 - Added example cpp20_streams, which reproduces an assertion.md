# #85 - Added example cpp20_streams, which reproduces an assertion. [Closed]

> Username: bveldhoen  
> Created at: 2023-03-15 21:30:07 UTC  
> Updated at: 2023-05-06 19:26:10 UTC  
> Closed at: 2023-05-06 19:26:10 UTC  
> Url: https://github.com/boostorg/redis/issues/85  

I've created both a PR and an issue as I wasn't sure what would be the preferred way to deal with this. Feel free to close either one.  
  
Please see this PR for a description:  
https://github.com/boostorg/redis/pull/84

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-03-16 07:57:09 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471474726  

Hi, thanks for reporting, I will have a look. Does the problem persist if you use only one thread.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-03-16 08:08:12 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471487763  

I have run the test on a single threaded `io_context` and there is no crash.

---

## Comment 3

> Username: bveldhoen  
> Created at: 2023-03-16 08:50:55 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471541278  

Indeed, I also can't reproduce using only 1 thread.

---

## Comment 4

> Username: bveldhoen  
> Created at: 2023-03-16 08:51:33 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471541989  

I've just added a commit to clean it up a bit.

---

## Comment 5

> Username: bveldhoen  
> Created at: 2023-03-16 09:32:18 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471599399  

The current workaround I'm using is by giving the redis_client its own ioc (instead of sharing it with other classes that use it), and that ioc runs on a single thread. However, this shouldn't be a requirement for using boost::redis, correct? In other words, redis_client and boost::redis should work with an ioc that runs on multiple threads, even though the strand ensures that calls to async_exec and its completion handler aren't executed concurrently.

---

## Comment 6

> Username: mzimbres  
> Created at: 2023-03-16 09:44:46 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471619798  

Correct, it shoud be possible to use boost.redis with multi-threaded io_contexts as well. There is some concurrent issue there that I couldn't spot yet, probably in the use of strand.

---

## Comment 7

> Username: mzimbres  
> Created at: 2023-03-16 10:42:54 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471709770  

I have rewritten your example using some facility functions I am developing in another branch and couldn't also see any crash: https://gist.github.com/mzimbres/b4e25f4b95a1c4f5c8ce8aa29dde6352.

---

## Comment 8

> Username: bveldhoen  
> Created at: 2023-03-16 13:02:32 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1471915145  

I've added a new branch with some changes to the examples, see here:  
https://github.com/bveldhoen/redis/tree/83-fix-reconnect-loop-in-the-subscriber-example-with-streams-example  
  
Points of attention:  
  
* I've had to add a call to req_.clear() in cpp17_streams. Without it, the example didn't function properly on my machine (it took a long time before the writes to the console became visible).  
* The problem seemed to go away in cpp20_streams when using the new redis::async_run method. Other changes (i.e. members for req and resp) didn't make any difference. See the changes in the top commit for the fix for cpp20_streams.  
* Going forward, I think the name cpp17_streams and the changed implementation are better suitable. cpp20_streams can be removed.

---

## Comment 9

> Username: mzimbres  
> Created at: 2023-03-16 15:29:24 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1472201604  

> I've had to add a call to req_.clear() in cpp17_streams.  
  
Yes, that is required. I have forgotten in my version of it.  
  
> The problem seemed to go away in cpp20_streams when using the new redis::async_run method.  
  
Possibly because `redis::async_run` ensures correct executor usage.  
  
> Other changes (i.e. members for req and resp) didn't make any difference.  
  
These changes are meant to avoid unnecessary dynamic allocations.  
  
> Going forward, I think the name cpp17_streams and the changed implementation are better suitable. cpp20_streams can be removed.  
  
Ok. Do you need reconnection and health checks?

---

## Comment 10

> Username: bveldhoen  
> Created at: 2023-03-16 16:33:14 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1472311766  

Yes, that would be helpful indeed, thanks!

---

## Comment 11

> Username: mzimbres  
> Created at: 2023-03-16 20:14:51 UTC  
> Url: https://github.com/boostorg/redis/issues/85#issuecomment-1472676475  

Note to self: Health checks may be a bad thing to use with `XREAD` on blocking mode.
