# #27 - Logical dead lock when we don't want to reuse the connection [Closed]

> Username: nejati-deriv  
> Created at: 2022-09-22 10:11:44 UTC  
> Updated at: 2022-10-15 19:13:49 UTC  
> Closed at: 2022-10-15 19:13:49 UTC  
> Url: https://github.com/boostorg/redis/issues/27  

This is not a bug, but a property of current design, there is a logical dead lock in the scenarios where we don't want to reconnect we just want to cancel and join ongoing tasks that are using connection.  
  
- A connection is disconnected  
- We cancel current pending requests with coon.cancel(operation::exec)  
- Any new call to async_exec at this points leads to dead lock because will wait for reconnect.  
  
What I can do is to keep connection status somewhere and check it before calls to async_exec.  
  
This happens because the tasks that I cancel on Redis disconnect can be canceled due to other reasons too and in the last step they send a message to Redis:  
```C++  
auto req = aedis::resp3::request{};  
req.push("XADD", "connection", "*", "event", "disconnect", "uuid", uuid);  
co_await connection_.async_exec(req, aedis::adapt(), asio::use_awaitable);  
```  
I expect to get an exception here when Redis itself is disconnected and task get cancelled without any need to for checking connection status.  
  
Another scenario that this can happens is that when I create a new tasks when Redis is disconnected and waited for ongoing tasks. It  starts to send requests with async_exec, I expect an exception here without a need to status check.  
  
This all happens because of the nature of connection, it is designed in a way it can be reconnected but it leads to a bit labor when we want to use it in a way that its a reliable connection if it is disconnected we should clean and return with an error.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-09-24 13:23:35 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1256967953  

Fixed in commit https://github.com/mzimbres/aedis/commit/85ba41ae5a12524ab60b49035e4f6a0bf1e8f473

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-09-25 20:52:12 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1257277272  

I have added you to the Acknowledgent section here https://github.com/mzimbres/aedis/blob/master/README.md#acknowledgement. Hope it is ok for, if not please let me know.

---

## Comment 3

> Username: nejati-deriv  
> Created at: 2022-09-26 05:10:02 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1257489282  

> I have added you to the Acknowledgent section here https://github.com/mzimbres/aedis/blob/master/README.md#acknowledgement. Hope it is ok for, if not please let me know.  
  
Thanks, glad to be of help.

---

## Comment 4

> Username: mzimbres  
> Created at: 2022-10-02 08:03:08 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1264579482  

Can we close this?

---

## Comment 5

> Username: nejati-deriv  
> Created at: 2022-10-03 13:58:07 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1265485030  

The first part of the problem (waiting for requests after connection error) is fixed, thanks.  
  
The other part of the issue is a natural consequence of your design, because you chose the connection be reusable in case of a disconnect or error, sending any request on a closed connection would lead to dead lock (if we don't want to reconnect the connection).  
If a closed connection (with error) is in an unspecified state, reconnecting it might not be helpful, for example if the last requests might have been applied on Redis server but didn't received a response on connection (what we want to do with those requests ?). if such scenarios are possible then recovering a connection is more than just a reconnect operation and I think it needs users intervention.

---

## Comment 6

> Username: mzimbres  
> Created at: 2022-10-03 18:01:04 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1265828148  

> The other part of the issue is a natural consequence of your design,  
> because you chose the connection be reusable in case of a disconnect  
> or error, sending any request on a closed connection would lead to  
> dead lock (if we don't want to reconnect the connection).  
  
We can also let `request::config::close_on_connection_lost` be true by default, so that users wanting reconnect support would have to set it explicitly. Would that lower your concerns?  
  
Without reconnection support, every call to `async_exec` would need some kind of retry on user side, leading to boilerplate and convoluted code. I want to avoid that. Most Redis clients in other languages go even further and provide built-in support for _automatic_ reconnection.  
  
> If a closed connection (with error) is in an unspecified state,  
> reconnecting it might not be helpful, for example if the last  
> requests might have been applied on Redis server but didn't received  
> a response on connection (what we want to do with those requests ?).  
  
It depends on the command, it would be probably ok to resend read-only commands like `GET` but dangerous for write commands like `SET`. This is something only the user can say. To support this, I will add a `retry` property to the request class.  
  
> if such scenarios are possible then recovering a connection is more  
> than just a reconnect operation and I think it needs users  
> intervention.  
  
Yes, I think Aedis should offer flags to cover all those cases, with safe defaults.

---

## Comment 7

> Username: mzimbres  
> Created at: 2022-10-03 18:10:57 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1265839518  

See #33.

---

## Comment 8

> Username: mzimbres  
> Created at: 2022-10-13 20:00:09 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1278115990  

The retry flag mentioned above was implemented in https://github.com/mzimbres/aedis/commit/770e224917e4f3afea2b244cf1448d19f052873c. Does it suit your needs?

---

## Comment 9

> Username: nejati-deriv  
> Created at: 2022-10-14 12:22:02 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1278934069  

> The retry flag mentioned above was implemented in [770e224](https://github.com/mzimbres/aedis/commit/770e224917e4f3afea2b244cf1448d19f052873c). Does it suit your needs?  
  
Yes, kind of.  
The combination that I use is: `{ .cancel_on_connection_lost = true, .coalesce = true, .cancel_if_not_connected = true, .retry = false }`, although the correct behavior in my scenario is that if the connection is connecting for the first time, requests can queue up until connection connect or fail. which I think Is not possible with current configuration.  
  
And form an ergonomic point of view it looks a bit strange to write:  
```C++  
auto req = aedis::resp3::request{ { true, true, true, false } };  
```  
And it is quite hard to think about these combinations (It seems there should be a better way of achieving the same behavior)

---

## Comment 10

> Username: mzimbres  
> Created at: 2022-10-15 10:55:17 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1279720007  

>  although the correct behavior in my scenario is that if the connection is connecting for the first time, requests can queue up until connection connect or fail. which I think Is not possible with current configuration.  
  
In this case I would set `cancel_if_not_connected = false`, then when `async_run` completes for the first time you set it to `true` in your requests. This looks more like a application specific problem than something Aedis could provide.

---

## Comment 11

> Username: mzimbres  
> Created at: 2022-10-15 11:07:37 UTC  
> Url: https://github.com/boostorg/redis/issues/27#issuecomment-1279722239  

> And form an ergonomic point of view it looks a bit strange to write:  
  
Agree, I am thinking about how to improve it, perhaps by using bit flags ...  
  
>And it is quite hard to think about these combinations (It seems there should be a better way of achieving the same behavior)  
  
`cancel_on_connection_lost`, `coalesce` and `cancel_if_not_connected` are fully independent from each other. `coalesce` is provided mostly for testing purposes and in user code it should be probably always `true`. `retry` on the other hand makes only sense if `cancel_on_connection_lost = false`. The important thing is to have meaningful defaults, I am still not sure whether `cancel_on_connection_lost` should be `true` or `false`. If I let it `true` users will tend to implement retry mechanisms on their side without noticing the connection built-in support for that.
