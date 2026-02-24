# #331 - Rework async_receive to consume all elements in the channel [Closed]

> Username: mzimbres  
> Created at: 2025-10-13 20:47:41 UTC  
> Updated at: 2026-01-18 15:14:55 UTC  
> Closed at: 2026-01-18 15:14:54 UTC  
> Url: https://github.com/boostorg/redis/issues/331  

As I work on the `generic_flat_response` implementation I noticed that `async_receive` and its sync counterpart `receive` are too cumbersome and inefficient to work with.  At the moment the general for of the receive loop looks like this  
  
```cpp  
for (error_code ec;;) {  
   conn->receive(ec);  
   if (ec == error::sync_receive_push_failed) {  
      ec = {};  
      co_await conn->async_receive(asio::redirect_error(asio::use_awaitable, ec));  
   }  
  
   if (ec)  
      break;  // Connection lost, break so we can reconnect to channels.  
  
   ...  
  
   consume_one(resp);  
}  
```  
  
The `async_receive` function does not actually receive a response, it just removes its size from the channel. However it does not make sense to consume only one message at time since the response passed to `conn.set_receive_response()` might contain multiple pushes (i.e. the channel size). If we can make `async_receive` consume/accumulate all elements in the channel, the loop gets simpler and there is no need for the `consume_one` function that is inefficient since it rotates the response (any performance gain made by `generic_flat_response` will be eclipsed by these rotations). The general form of the loop consuming pushes would become  
  
```cpp  
for (error_code ec;;) {  
   // Accumulates all sizes in the channel.  
   co_await conn->async_receive2(asio::redirect_error(asio::use_awaitable, ec));  
   if (ec)  
      break;  // Connection lost, break so we can reconnect to channels.  
  
   for (auto const& elem: resp)  
      // Process the node.  
  
   resp.clear();  
}  
```  
  
Things to do  
  
  - Add an `async_receive2` function that consumes all messages in the channel.  
  - Deprecate `async_receive`, `receive` and `consume_one`.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-13 21:44:02 UTC  
> Updated at: 2025-10-13 21:49:41 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3399153894  

ACTUALLY. I had been thinking a lot about this and in your same line of thought. My use case was that I tried to implement pubsub state restoration (AKA you SUBSCRIBE in config::setup), and it doesn't work because async_receive gets cancelled on reconnection.  
  
What about making async_receive2 return a size with the number of pushes received? This makes it reliable, since you can receive new pushes to the push response while you're processing the pushes already received.  
  
I believe a channel is not the best structure here, and better use ~a vector~ a counter and a timer as a condition variable.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-10-13 21:44:35 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3399154834  

I also believe we shouldn't cancel async_receive2 on reconnection. We should encourage people to use the setup request, instead.

---

## Comment 3

> Username: criatura2  
> Created at: 2025-10-14 07:18:47 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3400440450  

> What about making async_receive2 return a size with the number of pushes received?  This makes it reliable, since you can receive new pushes to the push response while you're processing the pushes already received.  
  
The number of pushes received is already contained in the `generic_response` (i.e. the number of nodes with depth 0).  The read (wire) size on the other hand can't be read from the response.  
  
I believe however that there is a race on the `async_receive` completion value  
  
  1. The reader calls `async_send/send`.  
  2. The `async_receive` completion is enqueued for execution.  
  3. The reader process another pushes adding them to the response (passed to `conn.set_receive_response()`).  
  4. After processing all its data the reader performs another `async_read_some` releasing the event loop.  
  5. `async_receive` completes with the number of bytes that does not correspond to what is contained in the response.  
  
Therefore I believe `async_receive2` should not have a completion value i.e. `f(error_code)`  
  
> I believe a channel is not the best structure here, and better use a vector a counter and a timer as a condition variable.  
  
I think a typical condition variable wouldn't work because we need the notify call to block if a specific number of bytes is reached.  We can do that with a channel but we don't need a sequence of values, only one bytes counter. We need the ability to notify the other side without blocking If the maximum number of bytes hasn't been reached, otherwise it notifies and blocks. It should also not be possible to miss events which happens to timers, in `async_exec` I use a channel with size 1.  
  
> I also believe we shouldn't cancel async_receive2 on reconnection. We should encourage people to use the setup request, instead.  
  
Agree, the problem is that setup requests can't deal with channels subscribed after `async_run` was called. If we don't cancel, users will never have the opportunity to resubscribe on reconnection.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-10-17 14:57:16 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3415936507  

> I believe however that there is a race on the async_receive completion value  
  
With the approach you're suggesting, I think this is somehow possible, too. At least, with this kind of approach:  
  
```cpp  
for (error_code ec;;) {  
   // Accumulates all sizes in the channel.  
   co_await conn->async_receive2(asio::redirect_error(asio::use_awaitable, ec));  
   if (ec)  
      break;  // Connection lost, break so we can reconnect to channels.  
  
   for (auto const& elem: resp)  
      co_await websocket.async_write(...); // async operation  
  
   resp.clear();  
}  
```  
  
With this setup, we might get "bogus" completions of `async_receive2`:  
  
* `async_receive2` completes because 5 pushes arrived from the server.  
* Processing them involves async operations, so more pushes might be received from the server in the meanwhile. Let's say by the time we arrived to the last push, another push is received.  
* The loop would continue, consuming that last push (* see note).  
* `async_receive2` is called again. There is a push size in the channel, so we complete immediately. But there is nothing in the response, because we consumed it.  
  
Aside from that, the above code is probably UB, because we're mutating the response object (potentially invalidating iterators) while iterating.  
  
Also, protecting this with a strand is complex. You'd need to run everything under the connection's strand, since the receive response is accessed by the connection object.  
  
I understand that this approach (vs passing an `any_adapter` object to `async_receive`) was chosen for performance, wasn't it? I'm concerned that most users might not realize that the above code is UB.

---

## Comment 5

> Username: mzimbres  
> Created at: 2025-10-18 08:50:37 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3418037292  

There are some constraints on the receiver side, one is that other async functions from the same connection shouldn't be used. The other is that the response should be consumed synchronously without yielding because, as you notice, the reader might read more pushes while the user is consuming the response. We can't really avoid this problem and have a good performance, for example, say async_receive completes and the response is being processed asynchronously. If a new push arrives the reader will have to block. A better approach for the user is to make a copy of the response and process it async if required.

---

## Comment 6

> Username: mzimbres  
> Created at: 2025-10-18 20:22:00 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3418792415  

For reference, I implemented the `async_receive2` [here](https://github.com/boostorg/redis/blob/f6e166071e7ca3efee37557a22291d74f8060f78/include/boost/redis/connection.hpp#L862-L885).

---

## Comment 7

> Username: anarthal  
> Created at: 2025-10-21 10:05:02 UTC  
> Updated at: 2025-10-21 10:06:35 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3425752994  

I see. Looks simple enough. We should test whether this supports `cancel_after`, as I recall a comment on Slack stating that as a problem when using `deferred`.  
  
I get that the channel now serves these purposes:  
  
1. Stores push sizes for compatibility with `async_receive` (v1).  
2. Allows the caller of `async_receive2` to wait for pushes to be available.  
3. Allows the reader to block if there were too many undelivered pushes.  
  
I recall from my benchmark experiment that calling into the channel infrastructure has a non-zero cost, even when not using the async functions. Point 1. above does require a channel, but if we get rid of it (long term), points 2. and 3. can be implemented with timers. What do you think?  
  
In the line of what I'm saying, it may make sense to not specify `async_receive2` API in term of channels. Currently, when reconnection happens, we return an `asio::experimental::error::channel_cancelled`. May it make sense to translate this error to something more specific, like a new `redis::error::reconnection` (or some better name)?  
  
We definitely need docs on "don't do async things with the response", as I didn't know myself that this could be a problem.  
  
Do you want me to implement this on top of the code you suggested? Or are you implementing this yourself already?

---

## Comment 8

> Username: mzimbres  
> Created at: 2025-10-22 20:00:44 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3434014197  

> I see. Looks simple enough. We should test whether this supports cancel_after, as I recall a comment on Slack stating that as a problem when using deferred.  
  
It doesn't indeed, I had to comment the test that uses it with `async_receive`.  
  
> I get that the channel now serves these purposes:  
>  
>    1. Stores push sizes for compatibility with async_receive (v1).  
>    2. Allows the caller of async_receive2 to wait for pushes to be available.  
>    3. Allows the reader to block if there were too many undelivered pushes.  
>  
> I recall from my benchmark experiment that calling into the channel infrastructure has a non-zero cost, even when not using the async functions.  Point 1. above does require a channel, but if we get rid of it (long term), points 2. and 3. can be implemented with timers. What do you think?  
  
I think timers won't work because the waiting end might miss the notification e.g. if `cancel()` is called and `async_wait` wasn't installed yet.  I think the channel API is close to what we need, however we don't need a sequence of values but just a counter. What kind of async primitive does this? For example  
  
```cpp  
struct notifier {  
  // If the counter is less than a threshold increases it by one and unblocks  
  // any async_wait, otherwise returns false.  
  bool try_notify();  
  
  // Waits for async_wait to clear the counter.  
  auto async_notify();  
  
  // Wait the counter to become != 0 and resets it to zero.  
  auto async_wait();  
};  
```  
  
Notice I think we can't avoid that `async_receive2` completes but the response is empty, for example  
  
  1. Subscriber calls `async_receive2`.  
  2. Reader receives a push and calls `try_send/async_send`.  
  3. Subscriber `async_receive2` completes, the deferred operation clears the channel and the completion is enqueued for execution. (I don't know how scheduling works for deferred ops).  
  4. Reader is scheduled again, receives another push and calls `try_send`.  
  5. Subscriber is executed, all responses are processed and `async_receive2` is called again.  
  6. `async_receive2` completes because of the push sent in 4. could not have been consumed in 3. (of course) but the response was consumed in 5. therefore the response will be empty here.  
  
> In the line of what I'm saying, it may make sense to not specify async_receive2 API in term of channels. Currently, when reconnection happens, we return an asio::experimental::error::channel_cancelled. May it make sense to translate this error to something more specific, like a new redis::error::reconnection (or some better name)?  
  
I agree. But if we ever implement persistent PUBSUB I might not need to cancel at all.  I am wondering if the user ever needs to know a reconnection took place except for the log lines.  
  
> We definitely need docs on "don't do async things with the response", as I  
> didn't know myself that this could be a problem.  
>  
> Do you want me to implement this on top of the code you suggested? Or are you  
> implementing this yourself already?  
  
I have made some progresses but as part of the generic flat response PR. I have to split it in two PRs but I am really struggling with time. I would be grateful for any help, you could check the diff of [this](https://github.com/boostorg/redis/tree/263-marcelo) branch with the develop (I have rebased now) and take the relevant parts (ignore changes related to the `generic_flat_response`). I would be great help, we can discuss further on slack, let me know. Thanks.

---

## Comment 9

> Username: anarthal  
> Created at: 2025-10-23 18:59:39 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3438653000  

> It doesn't indeed, I had to comment the test that uses it with async_receive.  
  
Good to see our that our tests detect things.   
  
> I think timers won't work because the waiting end might miss the notification e.g. if cancel() is called and async_wait wasn't installed yet.  
  
I don't think this is necessarily true. A timer alone won't do it, but a timer and a counter shouldn't miss any notification. This is essentially what we already do today in the writer.  
  
Re: the notifier you're proposing. Instead of single-message methods, I suggest using "bulk" methods. At the end of the day, a message read by the reader might contain any number of pushes, N. What I'm proposing is:  
  
* The reader gets a message (as of today).  
* We parse everything it contains. No notifications made at this point yet. Pushes are stored in the response passed by the user. This yields the number of parsed pushes, N.  
* We wake any receive tasks waiting.  
* If we exceeded the "channel capacity", we wait.  
  
Something like  
  
```cpp  
class notifier {  
    timer_type recv_timer_;  
    timer_type send_timer_;  
    std::size_t pending_pushes_;  
public:  
    auto async_receive() {  
        if (pending_pushes_ == 0u) {  
             co_await recv_timer_.async_wait(); // wait for pushes  
        }  
        pending_pushes_ = 0u; // mark all pushes as consumed  
        send_timer_.cancel(); // if the sender was blocked because of a "full channel", unblock it  
    }  
  
    auto async_send(std::size_t N) {  
        pending_pushes_ += N;  
        recv_timer_.cancel(); // notify the receiver  
        if (pending_pushes_ >= channel_size) {  
            co_await send_timer_.async_wait(); // oops, "channel" full, wait until the user consumes some pushes  
        }  
    }  
};  
```  
  
(expressed with co_await for simplicity, should be done in FSMs).  
  
This might not be as useful ATM because we still need to store all the sizes for the v1 version.  
  
> I agree. But if we ever implement persistent PUBSUB I might not need to cancel at all. I am wondering if the user ever needs to know a reconnection took place except for the log lines.  
  
Hm. How difficult do you think this could get? I can try to code a prototype for this. Do you think it could be an option to get this done before `async_receive2` so we can avoid the cancellation altogether? I don't think we're on time to ship this for 1.90, and there's time before 1.91.  
  
> I have made some progresses but as part of the generic flat response PR. I have to split it in two PRs but I am really struggling with time. I would be grateful for any help, you could check the diff of [this](https://github.com/boostorg/redis/tree/263-marcelo) branch with the develop (I have rebased now) and take the relevant parts (ignore changes related to the generic_flat_response). I would be great help, we can discuss further on slack, let me know. Thanks.  
  
I'm happy to get time into this, it motivates me.

---

## Comment 10

> Username: anarthal  
> Created at: 2025-10-23 19:55:00 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3438878929  

I'm actually looking at how the Valkey GLIDE client does pubsub restoration, and they seem to not allow dynamic changes: https://github.com/valkey-io/valkey-glide/wiki/General-Concepts#pubsub-support. Not saying we need to do it like this.  
I'm also reading about Redis cluster, which I was completely unaware of and... wow - that's really complex.

---

## Comment 11

> Username: mzimbres  
> Created at: 2025-10-24 21:34:43 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3444991997  

Just a short reaction while I am still thinking:   
  
- I think the notifier should not count pushes but bytes because that is the only way for the user to impose its constraints on memory consumption. The number of pushes has little meaning.  
  
- I still think a sync `try_send` is needed for better performance, otherwise all notification will pay the price of one rescheduling. If it happens too often it will degrade performance compared to the current implementation which will never reschedule if the receiver end consumes fast enough.

---

## Comment 12

> Username: anarthal  
> Created at: 2025-10-24 22:39:54 UTC  
> Url: https://github.com/boostorg/redis/issues/331#issuecomment-3445130022  

> I think the notifier should not count pushes but bytes because that is the only way for the user to impose its constraints on memory consumption. The number of pushes has little meaning.  
  
Looks fair, better than what we have now. We can let the user configure it.  
  
> I still think a sync try_send is needed for better performance, otherwise all notification will pay the price of one rescheduling. If it happens too often it will degrade performance compared to the current implementation which will never reschedule if the receiver end consumes fast enough.  
  
I wrote it as async methods to make my point, but with timers and a sans-io approach you shouldn't get the rescheduling.
