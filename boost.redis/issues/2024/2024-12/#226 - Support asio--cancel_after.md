# #226 - Support asio::cancel_after [Closed]

> Username: anarthal  
> Created at: 2024-12-19 11:58:12 UTC  
> Updated at: 2025-10-06 16:11:26 UTC  
> Closed at: 2025-10-06 16:11:26 UTC  
> Url: https://github.com/boostorg/redis/issues/226  

This is a very handy token, but it requires the initiation function object to have a bound executor. The following should work:  
  
```cpp  
redis::connection conn (ctx);  
co_await conn.async_exec(req, res, asio::cancel_after(10s));  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-12-22 20:42:32 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2558592600  

I tried to implement cancelation for async_exec here: https://github.com/boostorg/redis/blob/d910557db43f66d3966040a3dd8b72648ddd993b/include/boost/redis/detail/connection_base.hpp#L149  
  
At the moment I am considering removing as it is hard and I am not sure there is a good use cases for it. Do you think this is worth doing?

---

## Comment 2

> Username: anarthal  
> Created at: 2024-12-22 20:46:26 UTC  
> Updated at: 2024-12-22 20:46:43 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2558593786  

Yes, there are good reasons. Without cancellation, you can't run a request with a timeout, for instance.

---

## Comment 3

> Username: mzimbres  
> Created at: 2025-05-11 10:11:47 UTC  
> Updated at: 2025-05-11 16:03:30 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2869669418  

I am trying to understand how `async_exec` cancellation should work. There are four states to consider  
  
  1. **waiting**: Request wasn't written to the socket yet.  
  2. **staged**: Request is being written to the socket i.e.  `async_write` is suspended.  
  3. **written**: The payload was written to the socket i.e. `async_write` completed with success. At this point the request is either waiting for a response if one is expected or done if none is expected.  
  4. **done**: Response arrived and completion is queued for completion.  
  
Taking that into account  
  
  - **total**: cancellation is only possible at 1.  
  - **partial**: can not be honored or is too difficult to implement. Should I ignore this type of cancellation (except when in 1.)?  
  - **terminal**: Can be honored most of the time except at 4. where the request was executed with success, in that case cancellation must be ignored?  
  
@anarthal: How does this work in Boost.Mysql?  
  
Some things are still not clear to me, will cancellation cause the operation to complete with an error code e.g. `operation_cancelled` or does it complete with success and I have to look at the cancellation state, for example, with `async_compose` I do  
  
```cpp  
self.get_cancellation_state().cancelled() != asio::cancellation_type_t::none;  
```  
  
@klemens-morgenstern, Please, chime in if you think I am misunderstanding cancellation. thanks.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-05-11 11:21:16 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2869774037  

You are correct. At 4 you should just ignore all cancellation requests.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-05-11 13:58:52 UTC  
> Updated at: 2025-05-11 13:59:11 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2869871459  

There's actually one more hidden case:  
  
 waiting: Request wasn't written to the socket yet.  
staged: Request is being written to the socket i.e. async_write is suspended.  
just after staged: request has been written successfully, but its handler hasn't run yet. If we are to wait for a response, `async_wait` hasn't been called yet.  
written: The payload was written to the socket i.e. async_write completed with success. At this point the request is either waiting for a response if one is expected or done if none is expected.  
done: Response arrived and completion is queued for completion.  
  
Detection goes like this:  
  
waiting: you need to check `self.get_cancellation_state()`  
staged: `async_write` will fail with `asio::error::operation_aborted`  
just after staged: you need to check `self.get_cancellation_state()`  
written: if you're waiting a response, the wait operation will finish with `asio::error::operation_aborted`. If you're using a timer, you might need to check `self.get_cancellation_state()` to disambiguate, since cancelling a timer causes the same error code.  
done: as Klemens said, you should just complete the operation here. Nothing to check.  
  
`asio::cancel_after` does terminal cancellation by default, so I'm inclined to think that's the one that is more important to support. I'd support `terminal` as you described, and maybe total/partial for requests that haven't been queued yet, as you proposed.  
  
In Boost.MySQL:  
  
* For `any_connection` (it does no queuing), only `terminal` is supported. It is implemented as a sans-io state machine that outputs what to do next, plus an `async_compose` that just does what the state machine says (https://github.com/boostorg/mysql/blob/90405e79e4590f142e28094eea1625f259ffeec4/include/boost/mysql/detail/engine_impl.hpp#L46). The `async_compose` loop checks `self.get_cancellation_state()`.  
* `connection_pool` supports all cancellation types because `async_get_connection` can be cancelled without side effects.  
  
I believe we could extend what you did in the multiplexer (great job BTW) to take most of the logic in `async_exec` outside of `async_compose`. Would you like me to experiment with that next week?

---

## Comment 6

> Username: mzimbres  
> Created at: 2025-05-11 16:01:07 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2869954640  

> I believe we could extend what you did in the multiplexer (great job BTW) to take most of the logic in async_exec outside of async_compose. Would you like me to experiment with that next week?  
  
Yes, go ahead, thanks. That is actually very important otherwise testing is very hard and limited. Notice that support for cancellation in `async_exec` will eliminate the need for [cancel_if_not_connected](https://github.com/boostorg/redis/blob/1060733b8456fdbe0b1468eced7a787547cf8a94/include/boost/redis/request.hpp#L60C12-L60C35), which will come as breaking change.

---

## Comment 7

> Username: anarthal  
> Created at: 2025-05-12 10:11:32 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2871910455  

Why is that? It looks like `cancel_if_not_connected` serves a different purpose than regular cancellation, right? `cancel_if_not_connected` is like "do you want to cancel your request or wait for a connection to be established"? While regular cancellation is a tool to say "hey cancel this request, regardless of what the connection is doing". What do you think?

---

## Comment 8

> Username: criatura2  
> Created at: 2025-05-12 15:03:38 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2872942743  

The problem is that `cancel_if_not_connected` does not do what it claims.  Being connected should mean a command can be executed but all the implementation does is to check whether the socket is open, which is not strong enough.  
  
If cancellation is supported users like [this](https://github.com/boostorg/redis/issues/164) won't need to know whether the connection stands since they can simply send the request with a timeout. If the timer fires it is not connected (or the timeout was too short).

---

## Comment 9

> Username: anarthal  
> Created at: 2025-05-12 15:08:46 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2872962886  

I see what you mean. I plan on replacing that, but I want to convert most of the code that should modify such state to sans-io, so I don't break things accidentally.

---

## Comment 10

> Username: mzimbres  
> Created at: 2025-05-17 17:03:25 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2888494595  

Note for the future: Cancellation support will significantly simplify the [health-check](https://github.com/boostorg/redis/blob/develop/include/boost/redis/detail/health_checker.hpp) implementation and reduce the total number or parallel operations [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/detail/runner.hpp#L152-L157).

---

## Comment 11

> Username: anarthal  
> Created at: 2025-05-17 17:18:48 UTC  
> Url: https://github.com/boostorg/redis/issues/226#issuecomment-2888500571  

Yes, I saw that yesterday.
